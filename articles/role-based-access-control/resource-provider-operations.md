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
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546323"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Erőforrás-szolgáltatói műveletek Azure Resource Manager

Ez a cikk felsorolja az egyes Azure Resource Manager erőforrás-szolgáltatók számára elérhető műveleteket. Ezek a műveletek [Egyéni szerepkörökben](custom-roles.md) használhatók, amelyek részletes [szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) biztosítanak az Azure-beli erőforrásokhoz. A műveleti karakterláncok formátuma a következő: `{Company}.{ProviderName}/{resourceType}/{action}`. Az erőforrás-szolgáltatói névterek Azure-szolgáltatásokhoz való leképezésének listáját lásd: az [erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/azure-services-resource-providers.md).

Az erőforrás-szolgáltatói műveletek folyamatosan fejlődnek. A legújabb műveletek beszerzéséhez használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) vagy [az az Provider Operation List műveletet](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AAD/domainServices/delete | Tartományi szolgáltatás törlése |
> | Műveletek | Microsoft.AAD/domainServices/oucontainer/delete | Ou-tároló törlése |
> | Műveletek | Microsoft.AAD/domainServices/oucontainer/read | Ou-tárolók olvasása |
> | Műveletek | Microsoft.AAD/domainServices/oucontainer/write | Ou-tároló írása |
> | Műveletek | Microsoft.AAD/domainServices/read | Tartományi szolgáltatások olvasása |
> | Műveletek | Microsoft.AAD/domainServices/replicaSets/delete | Fürt helyének törlése |
> | Műveletek | Microsoft.AAD/domainServices/replicaSets/read | Fürt helyének olvasása |
> | Műveletek | Microsoft.AAD/domainServices/replicaSets/write | Fürt helyének írása |
> | Műveletek | Microsoft.AAD/domainServices/write | Tartományi szolgáltatás írása |
> | Műveletek | Microsoft.AAD/locations/operationresults/read |  |
> | Műveletek | Microsoft.AAD/Operations/read |  |
> | Műveletek | Microsoft.AAD/register/action | Tartományi szolgáltatás regisztrálása |
> | Műveletek | Microsoft.AAD/unregister/action | Tartományi szolgáltatás regisztrációjának törlése |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.aadiam/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | microsoft.aadiam/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/Write | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft. aadiam/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Addons/operations/read | A támogatott RP-műveletek beolvasása. |
> | Műveletek | Microsoft.Addons/register/action | A megadott előfizetés regisztrálása a Microsoft. addons szolgáltatásban |
> | Műveletek | Microsoft.Addons/supportProviders/listsupportplaninfo/action | A megadott előfizetéshez tartozó aktuális támogatási csomag információit sorolja fel. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott Canonical-támogatási csomagot. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/read | A megadott Canonical támogatási csomag állapotának beolvasása. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/write | Hozzáadja a megadott Canonical-támogatási csomag típusát. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/action | Hozzon létre egy új erdőt a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | A megadott szolgáltatásnév összes kiszolgálójának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/alerts/read | Riasztások részleteinek beolvasása az erdőhöz, például a alertid, a riasztás dátuma, a riasztás legutóbbi észlelése, a riasztás leírása, a legutóbbi frissítés, a riasztási szint, a riasztási állapot, a riasztások hibaelhárítási hivatkozásai |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/configuration/read | Beolvassa az erdő szolgáltatás-konfigurációját. Példa: erdő neve, működési szint, tartománynév-kiosztási főkiszolgáló FSMO-szerepkör, séma-főkiszolgáló FSMO-szerepköre stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/delete | Töröl egy szolgáltatást, és az állapottal együtt a kiszolgálókat is. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/dimensions/READ | Lekéri az erdő tartományait és helyeit. Példa: állapotadatok, aktív riasztások, megoldott riasztások, tulajdonságok, például a tartomány működési szintje, erdő, infrastruktúra-főkiszolgáló, elsődleges tartományvezérlő, RID-főkiszolgáló stb.  |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/features/UserPreference/READ | Beolvassa az erdő felhasználói beállításait.<br>Példa – MetricCounterName, például ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>A felhasználói felületi diagramok beállításai stb. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/forestsummary/READ | Beolvassa az erdő összefoglalását az adott erdőhöz, például az erdő nevét, az erdő alá tartozó tartományok számát, a helyek és a helyek részleteit stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/premiumcheck/READ | Ez az API beolvassa a prémium bérlő összes bekészített ADDomainServices listáját. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/READ | A megadott szolgáltatásnév szolgáltatás részleteinek beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Lekérdezi a megadott szolgáltatásnév összes kiszolgálójának replikálási részleteit. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Lekéri a tartományvezérlők számát és a replikálási hibákat, ha vannak ilyenek. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Beolvassa a tartományvezérlők teljes listáját, valamint az adott erdő replikációs részleteit. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Servicemembers/művelet | Adja hozzá a kiszolgálói példányt a szolgáltatáshoz. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | A ADDomainService kiszolgáló regisztrációja során ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Egy adott szolgáltatás és bérlő kiszolgálójának törlése. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Write | Létrehozza vagy frissíti a bérlő ADDomainService-példányát. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/action | A bérlő konfigurációjának frissítése. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/read | A bérlő konfigurációjának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/write | Létrehoz egy bérlői konfigurációt. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/contents/read | A blobban tárolt ügynök-telepítési és regisztrációs naplók tartalmának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/read | Beolvassa az ügynök telepítési és regisztrációs naplóit a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/operations/read | Lekéri a rendszer által támogatott műveletek listáját. |
> | Műveletek | Microsoft.ADHybridHealthService/register/action | Regisztrálja a ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatót, és lehetővé teszi a ADHybrid Állapotfigyelő szolgáltatás erőforrás létrehozását. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/availabledeployments/READ | Lekéri az elérhető régiók listáját, amelyet a DevOps az ügyfél-incidensek támogatásához használ. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassword/read | Lekérdezi a helytelen jelszó-kísérletek listáját Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Beolvassa a blob SAS URI-t, amely tartalmazza az állapotot, és az újonnan várólistán lévő jelentési feladatának végleges eredményét az adott bérlő számára napi Felhasználónév/jelszó/IP-cím/felhasználóazonosító gyakorisága alapján. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Beolvassa a DevOps által beleegyezett bérlők listáját. Jellemzően ügyfélszolgálati támogatáshoz használatos. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/isdevops/read | Egy érték beolvasása, amely azt jelzi, hogy a bérlő DevOps-e, vagy sem. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/selectdevopstenant/READ | A kiválasztott fejlesztői Ops-bérlőhöz tartozó userid (ObjectId) frissítése. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Beolvassa az adott bérlő kiválasztott központi telepítését. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | A bérlői azonosító beolvasása után a bérlői tároló helye lesz. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Lekéri a földrajzi helyet, amelyről az adatok hozzáférnek. |
> | Műveletek | Microsoft.ADHybridHealthService/services/action | Egy szolgáltatási példány frissítése a bérlőben. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | A szolgáltatás neve alapján ellenőrizhető, hogy egy szolgáltatásnak van-e minden szükséges funkciója a szolgáltatás használatához. |
> | Műveletek | Microsoft.ADHybridHealthService/services/delete | Töröl egy szolgáltatási példányt a bérlőben. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/exporterrors/READ | Lekéri egy adott szinkronizálási szolgáltatás exportálási hibáit. |
> | Műveletek | Microsoft.ADHybridHealthService/services/exportstatus/read | Egy adott szolgáltatás exportálási állapotának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Riasztási visszajelzés küldése egy adott szolgáltatásról és kiszolgálóról. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/metricmetadata/READ | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/átlag/olvasás | Adott szolgáltatás esetében ez az API egy adott szolgáltatás metrikáinak átlagát kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Adott szolgáltatás esetén ez az API egy adott szolgáltatás metrikáinak összesített nézetét kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Szolgáltatás figyelési konfigurációjának hozzáadása vagy frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/READ | Lekéri egy adott szolgáltatás figyelési konfigurációit. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/monitoringconfigurations/írás | Szolgáltatás figyelési konfigurációinak hozzáadása vagy frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/premiumcheck/READ | Ez az API beolvassa a prémium szintű bérlőhöz tartozó összes beépített szolgáltatás listáját. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/olvasás | A bérlőben lévő szolgáltatási példányok beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/blobUris/olvasás | Minden kockázatos IP-jelentés URI-azonosítójának beolvasása az elmúlt 7 napban. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/részletek/olvasás | Az elmúlt 7 nap helytelen jelszavas hibával rendelkező leggyakoribb 50-felhasználók jelentésének beolvasása |
> | Műveletek | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Kockázatos IP-jelentést generál, és visszaadja a rá mutató URI-t. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/művelet | Létrehoz egy kiszolgálói példányt a szolgáltatásban. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/riasztások/olvasás | Egy kiszolgáló értesítéseinek beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | A kiszolgáló regisztrálása során a rendszer ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/datafreshness/READ | Az adott kiszolgáló esetében ez az API lekéri a kiszolgálók által feltöltött adattípusok listáját, valamint az egyes feltöltések legkésőbbi idejét. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/delete | Töröl egy kiszolgálói példányt a szolgáltatásban. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/READ | Beolvassa a szinkronizálási exportálási hiba részleteit egy adott szinkronizálási szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/mérőszámok/olvasás | Lekérdezi az összekötők listáját, és futtatja a profil nevét az adott szolgáltatás és szolgáltatás tagja számára. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/READ | A szolgáltatásban található kiszolgálópéldány beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/serviceconfiguration/READ | Egy adott bérlő szolgáltatás-konfigurációjának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/READ | Egy adott bérlő szolgáltatás-engedélyezési állapotának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/írás | Létrehoz egy szolgáltatási példányt a bérlőben. |
> | Műveletek | Microsoft.ADHybridHealthService/unregister/action | A ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Műveletek | Microsoft. Advisor/konfigurációk/írás | Konfiguráció létrehozása/frissítése |
> | Műveletek | Microsoft.Advisor/generateRecommendations/action | Javaslatok generálása |
> | Műveletek | Microsoft.Advisor/generateRecommendations/read | Javaslatok állapotának előállítása |
> | Műveletek | Microsoft.Advisor/metadata/read | Metaadatok beolvasása |
> | Műveletek | Microsoft.Advisor/operations/read | Lekéri a Microsoft Advisor műveleteit |
> | Műveletek | Microsoft. Advisor/javaslatok/elérhető/művelet | Új javaslat érhető el a Microsoft Advisor szolgáltatásban |
> | Műveletek | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/delete | Törlés mellőzése |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/read | Letiltások beolvasása |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/write | Eltávolítások létrehozása/frissítése |
> | Műveletek | Microsoft.Advisor/register/action | Regisztrálja a Microsoft Advisor előfizetését |
> | Műveletek | Microsoft.Advisor/suppressions/delete | Törlés mellőzése |
> | Műveletek | Microsoft.Advisor/suppressions/read | Letiltások beolvasása |
> | Műveletek | Microsoft. Advisor/Mellőzés/írás | Eltávolítások létrehozása/frissítése |
> | Műveletek | Microsoft.Advisor/unregister/action | A Microsoft Advisor-előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AlertsManagement/actionRules/delete | Művelet szabályának törlése egy adott előfizetésben. |
> | Műveletek | Microsoft.AlertsManagement/actionRules/read | A bemeneti szűrőkhöz tartozó összes művelet szabályának beolvasása. |
> | Műveletek | Microsoft.AlertsManagement/actionRules/write | Műveleti szabály létrehozása vagy frissítése egy adott előfizetésben |
> | Műveletek | Microsoft.AlertsManagement/alerts/changestate/action | A riasztás állapotának módosítása |
> | Műveletek | Microsoft. AlertsManagement/riasztások/diagnosztika/olvasás | A riasztáshoz tartozó összes diagnosztika beolvasása |
> | Műveletek | Microsoft.AlertsManagement/alerts/history/read | A riasztás előzményeinek beolvasása |
> | Műveletek | Microsoft.AlertsManagement/alerts/read | A bemeneti szűrők összes riasztásának beolvasása. |
> | Műveletek | Microsoft.AlertsManagement/alertsList/read | A bemeneti szűrők összes riasztásának beolvasása az előfizetések között |
> | Műveletek | Microsoft. AlertsManagement/alertsMetaData/READ | Riasztások beolvasása a bemeneti paraméterhez. |
> | Műveletek | Microsoft.AlertsManagement/alertsSummary/read | Riasztások összefoglalásának beolvasása |
> | Műveletek | Microsoft.AlertsManagement/alertsSummaryList/read | Riasztások összegzésének lekérése az előfizetések között |
> | Műveletek | Microsoft.AlertsManagement/Operations/read | A megadott műveletek beolvasása |
> | Műveletek | Microsoft.AlertsManagement/register/action | Regisztrálja az előfizetést a Microsoft riasztások kezeléséhez |
> | Műveletek | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Intelligens detektor riasztási szabályának törlése egy adott előfizetésben |
> | Műveletek | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Az intelligens detektor riasztási szabályainak beolvasása a bemeneti szűrőkhöz |
> | Műveletek | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Intelligens detektor riasztási szabályának létrehozása vagy frissítése egy adott előfizetésben |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/changestate/action | Az intelligens csoport állapotának módosítása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/history/read | Az intelligens csoport előzményeinek beolvasása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/read | A bemeneti szűrők összes intelligens csoportjának beolvasása |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationresults/read | A megadott műveleti eredmény információinak beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationstatuses/read | Lekéri a megadott műveleti állapot adatait. |
> | Műveletek | Microsoft.AnalysisServices/operations/read | A műveletek információinak lekérése |
> | Műveletek | Microsoft.AnalysisServices/register/action | Regisztrálja Analysis Services erőforrás-szolgáltatót. |
> | Műveletek | Microsoft.AnalysisServices/servers/delete | Törli a Analysis Server. |
> | Műveletek | Microsoft.AnalysisServices/servers/listGatewayStatus/action | A kiszolgálóhoz társított átjáró állapotának listázása. |
> | Műveletek | Microsoft.AnalysisServices/servers/read | A megadott Analysis Server információinak beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/servers/resume/action | A Analysis Server folytatása. |
> | Műveletek | Microsoft.AnalysisServices/servers/skus/read | A kiszolgáló rendelkezésre álló SKU-adatainak beolvasása |
> | Műveletek | Microsoft.AnalysisServices/servers/suspend/action | Felfüggeszti a Analysis Server. |
> | Műveletek | Microsoft.AnalysisServices/servers/write | Létrehozza vagy frissíti a megadott Analysis Server. |
> | Műveletek | Microsoft.AnalysisServices/skus/read | A SKU-ra vonatkozó információk beolvasása |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ApiManagement/checkNameAvailability/read | Ellenőrzi, hogy elérhető-e a megadott szolgáltatás neve |
> | Műveletek | Microsoft.ApiManagement/operations/read | A Microsoft. ApiManagement erőforráshoz elérhető összes API-művelet olvasása |
> | Műveletek | Microsoft.ApiManagement/register/action | Előfizetés regisztrálása a Microsoft. ApiManagement erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.ApiManagement/reports/read | Az időszakok, a földrajzi régió, a fejlesztők, a termékek, az API-k, a műveletek, az előfizetés és a byRequest által összesített jelentések beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/apis/delete | Törli az API Management Service-példány megadott API-ját. |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/delete | A megadott diagnosztika törlése egy API-ból. |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/read | Egy API összes diagnosztikát listázza. vagy beolvassa az azonosító alapján megadott API-diagnosztika részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/write | Létrehoz egy új diagnosztikát egy API-hoz, vagy frissíti a meglévőt. vagy frissíti a diagnosztika részleteit az azonosító alapján megadott API-hoz. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/delete | A megadott Megjegyzés törlése a hibából. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/read | Felsorolja a megadott API-hoz tartozó probléma összes mellékletét. vagy beolvassa az azonosítóval megadott API-hoz tartozó melléklettel kapcsolatos adatokat. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/write | Új mellékletet hoz létre a probléma egy API-ban vagy egy meglévő frissítése során. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/delete | A megadott Megjegyzés törlése a hibából. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/read | Felsorolja a megadott API-hoz kapcsolódó probléma összes megjegyzését. vagy beolvassa az azonosító alapján megadott API-hoz tartozó probléma megjegyzésének részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/write | Új Megjegyzés létrehozása a probléma számára egy API-ban vagy egy meglévő frissítése. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/delete | Törli a megadott problémát egy API-ból. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/read | Felsorolja a megadott API-hoz kapcsolódó összes problémát. vagy beolvassa a probléma részleteit az azonosító által meghatározott API-hoz. |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/write | Új probléma létrehozása egy API-hoz, vagy egy meglévő frissítése. vagy frissít egy meglévő problémát egy API-hoz. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/delete | Törli a megadott műveletet az API-ban. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/delete | A házirend-konfiguráció törlése az API-művelettel. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/read | A házirend-konfiguráció listájának beolvasása az API-művelet szintjén. vagy szerezze be a házirend-konfigurációt az API-művelet szintjén. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/write | Létrehozza vagy frissíti az API-művelet szintjének házirend-konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/delete | A házirend-konfiguráció törlése a műveleti szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/read | Házirend-konfiguráció beolvasása műveleti szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/write | Házirend-konfiguráció létrehozása műveleti szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/read | Felsorolja a megadott API-hoz tartozó műveletek gyűjteményét. vagy lekéri az azonosító alapján megadott API-művelet részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/delete | Válassza le a címkét a műveletből. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/read | Felsorolja a művelethez társított összes címkét. vagy a művelethez társított címke beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/write | Címke társítása a művelethez. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/write | Létrehoz egy új műveletet az API-ban, vagy frissíti a meglévőt. vagy frissíti a művelet részleteit az azonosítójában megadott API-ban. |
> | Műveletek | Microsoft.ApiManagement/service/apis/operationsByTags/read | A címkékkel társított műveletek gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/delete | Törli a házirend-konfigurációt az API-ban. |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/read | A szabályzat konfigurációjának beolvasása az API szintjén. vagy szerezze be a szabályzat konfigurációját az API szintjén. |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/write | Létrehozza vagy frissíti az API házirend-konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/delete | A szabályzat konfigurációjának törlése API-szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/read | A szabályzat konfigurációjának beolvasása API-szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/write | Házirend-konfiguráció létrehozása API-szinten |
> | Műveletek | Microsoft.ApiManagement/service/apis/products/read | Felsorolja az összes olyan terméket, amely az API részét képezi. |
> | Műveletek | Microsoft.ApiManagement/service/apis/read | Felsorolja az API Management Service-példány összes API-jait. vagy lekéri az azonosító alapján megadott API részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/delete | Eltávolítja az API összes kiadását, vagy törli a megadott kiadást az API-ban. |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/read | Az API összes kiadását listázza.<br>Egy API-kiadás jön létre, amikor egy API-változatot aktuálisan használ.<br>A kiadásokat a rendszer az előző változatokra történő visszaállításra is felhasználja.<br>Az eredmények lapozva lesznek, és a $top és a $skip paraméterek is korlátozva lesznek.<br>vagy egy API-kiadás részleteit adja vissza. |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/write | Új kiadás létrehozása az API-hoz. vagy frissíti az azonosító alapján megadott API kiadásának részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/delete | Egy API összes változatának eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/read | Felsorolja az API összes változatát. |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/delete | Törli a séma konfigurációját az API-ban. |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/read | A séma konfigurációjának beolvasása az API szintjén. vagy a séma konfigurációjának beolvasása az API szintjén. |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/write | Létrehozza vagy frissíti az API sémájának konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Az API-címke leírásának törlése. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Az API hatókörében lévő összes címke leírása. A hencegés hasonló modell van definiálva az API-szinten, de a címke hozzárendelhető a műveletekhez, vagy lekérheti a címke leírását az API hatókörében lévő tagDescription. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Címke leírásának létrehozása/frissítése az API hatókörében. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/delete | Válassza le a címkét az API-ból. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/read | Az API-hoz társított összes címke listája. vagy az API-hoz társított címke beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/write | Címke társítása az API-hoz. |
> | Műveletek | Microsoft.ApiManagement/service/apis/write | Új vagy frissíti a API Management Service-példány meglévő API-ját. vagy frissíti a API Management Service-példány megadott API-ját. |
> | Műveletek | Microsoft.ApiManagement/service/apisByTags/read | A címkékhez társított API-k gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/apiVersionSets/delete | Az adott API-verzió megadott készletének törlése. |
> | Műveletek | Microsoft.ApiManagement/service/apiVersionSets/read | Az API-verziók készleteit sorolja fel a megadott szolgáltatási példányban. vagy lekéri az azonosító alapján megadott API-verzió részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Verzió entitások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apiVersionSets/write | Egy API-verzió készletének létrehozása vagy frissítése. vagy frissíti az azonosító alapján megadott API-VersionSet adatait. |
> | Műveletek | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Frissíti a Virtual Network futó Microsoft. ApiManagement-erőforrásokat a frissített hálózati beállítások kiválasztásához. |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/delete | Az adott engedélyezési kiszolgáló példányának törlése. |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/read | A szolgáltatási példányon belül meghatározott engedélyezési kiszolgálók gyűjteményét listázza. vagy lekéri az azonosító alapján megadott engedélyezési kiszolgáló adatait. |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/write | Új engedélyezési kiszolgáló létrehozása vagy egy meglévő engedélyezési kiszolgáló frissítése. vagy frissíti az azonosító alapján megadott engedélyezési kiszolgáló adatait. |
> | Műveletek | Microsoft.ApiManagement/service/backends/delete | A megadott háttér törlése. |
> | Műveletek | Microsoft.ApiManagement/service/backends/read | Felsorolja a megadott szolgáltatási példányban található háttérrendszer-gyűjteményeket. vagy lekéri az azonosító alapján megadott háttér részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/backends/reconnect/action | Értesíti a APIM proxyt, hogy a megadott időkorlát után hozzon létre egy új kapcsolódást a háttérrendszer számára. Ha nincs időkorlát megadva, a rendszer 2 perces időkorlátot használ. |
> | Műveletek | Microsoft.ApiManagement/service/backends/write | Egy háttérrendszer létrehozása vagy frissítése. vagy frissít egy meglévő hátteret. |
> | Műveletek | Microsoft.ApiManagement/service/backup/action | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Műveletek | Microsoft.ApiManagement/service/caches/delete | Adott gyorsítótár törlése. |
> | Műveletek | Microsoft.ApiManagement/service/caches/read | Felsorolja a megadott szolgáltatási példány összes külső gyorsítótárának gyűjteményét. vagy lekéri az azonosító alapján megadott gyorsítótár részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/caches/write | Létrehoz vagy frissít egy külső gyorsítótárat az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott gyorsítótár részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/certificates/delete | Adott tanúsítvány törlése. |
> | Műveletek | Microsoft.ApiManagement/service/certificates/read | Felsorolja a megadott szolgáltatási példányban található összes tanúsítvány gyűjteményét. vagy lekéri az azonosító alapján megadott tanúsítvány részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/certificates/write | Létrehozza vagy frissíti a háttérrel való hitelesítéshez használt tanúsítványt. |
> | Műveletek | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Eltávolítja a megadott tartalmi elemeket. |
> | Műveletek | Microsoft.ApiManagement/service/contentTypes/contentItems/read | A tartalmi elemek listáját vagy a tartalom elem részleteit adja vissza. |
> | Műveletek | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Új tartalmi elemek létrehozása vagy a megadott tartalmi elemek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/contentTypes/read | A tartalomtípusok listáját adja vissza. |
> | Műveletek | Microsoft.ApiManagement/service/delete | API Management szolgáltatás példányának törlése |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/delete | Törli a megadott diagnosztikát. |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/read | Felsorolja az API Management Service-példány összes diagnosztikát. vagy lekéri az azonosító alapján megadott diagnosztika részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/write | Létrehoz egy új diagnosztikát, vagy frissíti a meglévőt. vagy frissíti az azonosító alapján megadott diagnosztika részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/művelet | Az átjáró konfigurációjának beolvasása. vagy frissíti az átjáró szívverését. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/törlés | Adott átjáró törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/kulcsok/művelet | Az átjáró kulcsainak beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/olvasás | A Service instance szolgáltatásban regisztrált átjárók gyűjteményét listázza. vagy beolvassa az azonosító által megadott átjáró részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/regeneratePrimaryKey/művelet | Újragenerálta az elsődleges átjáró kulcsát a invalidationg létrehozott jogkivonatokkal. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/regenerateSecondaryKey/művelet | Újragenerálja a másodlagos átjáró kulcsát a vele létrehozott jogkivonatok invalidationg. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/jogkivonat/művelet | Az átjáróhoz tartozó megosztott hozzáférés engedélyezési jogkivonatának beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/írás | Létrehoz vagy frissít egy átjárót az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott átjáró részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/getssotoken/action | Beolvassa az SSO-tokent, amely bejelentkezhet API Management szolgáltatásbeli örökölt portálra rendszergazdaként |
> | Műveletek | Microsoft.ApiManagement/service/groups/delete | Törli a API Management szolgáltatási példány adott csoportját. |
> | Műveletek | Microsoft.ApiManagement/service/groups/read | A szolgáltatási példányon belül definiált csoportok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott csoport részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/delete | Meglévő felhasználó eltávolítása meglévő csoportból. |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/read | A csoporthoz társított felhasználói entitások gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/write | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Műveletek | Microsoft.ApiManagement/service/groups/write | Létrehoz vagy frissít egy csoportot. vagy frissíti az azonosító alapján megadott csoport részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/delete | Törli a megadott identitás-szolgáltatói konfigurációt. |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/read | Felsorolja a megadott szolgáltatási példányban konfigurált identitás-szolgáltató gyűjteményét. vagy beolvassa a megadott szolgáltatási példányban konfigurált identitás-szolgáltató konfigurációs részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/write | Létrehozza vagy frissíti a IdentityProvider-konfigurációt. vagy frissít egy meglévő IdentityProvider-konfigurációt. |
> | Műveletek | Microsoft.ApiManagement/service/issues/read | Felsorolja a megadott szolgáltatási példányban felmerülő problémák gyűjteményét. vagy API Management probléma részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/locations/networkstatus/read | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás a helytől függ. |
> | Műveletek | Microsoft.ApiManagement/service/loggers/delete | A megadott naplózó törlése. |
> | Műveletek | Microsoft.ApiManagement/service/loggers/read | A megadott szolgáltatási példányban lévő naplózók gyűjteményét listázza. vagy beolvassa az azonosító alapján megadott naplózó részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/loggers/write | Egy naplózó létrehozása vagy frissítése. vagy frissít egy meglévő naplózó. |
> | Műveletek | Microsoft.ApiManagement/service/managedeployments/action | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/networkstatus/read | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás függ. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/action | Értesítés küldése egy megadott felhasználónak |
> | Műveletek | Microsoft.ApiManagement/service/notifications/read | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy beolvassa az azonosító által megadott értesítés részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Eltávolítja az e-mailt az értesítési listából. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Beolvassa az értesítésre előfizetett értesítő címzett e-mailek listáját. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Hozzáadja az e-mail-címet az értesítés címzettjeinek listájához. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Eltávolítja a API Management felhasználót az értesítési listából. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Beolvassa az értesítésre előfizetett értesítési címzett felhasználó listáját. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Hozzáadja a API Management felhasználót az értesítés címzettjeinek listájához. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/write | API Management közzétevő értesítésének létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/delete | Törli a API Management szolgáltatás példányának adott OpenID Connect-szolgáltatóját. |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/read | Az összes OpenId Connect-szolgáltató listája. vagy lekéri az adott OpenID Connect-szolgáltatót. |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/write | Létrehozza vagy frissíti az OpenID Connect-szolgáltatót. vagy frissíti az adott OpenID Connect-szolgáltatót. |
> | Műveletek | Microsoft.ApiManagement/service/operationresults/read | A hosszú ideig futó művelet aktuális állapotának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/policies/delete | Törli az API Management szolgáltatás globális házirend-konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/policies/read | Az API Management szolgáltatás összes globális házirend-definícióját listázza. vagy szerezze be az API Management szolgáltatás globális szabályzatának definícióját. |
> | Műveletek | Microsoft.ApiManagement/service/policies/write | Létrehozza vagy frissíti az API Management szolgáltatás globális házirend-konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/policy/delete | A házirend-konfiguráció törlése a bérlői szinten |
> | Műveletek | Microsoft.ApiManagement/service/policy/read | Házirend-konfiguráció beolvasása a bérlői szinten |
> | Műveletek | Microsoft.ApiManagement/service/policy/write | Házirend-konfiguráció létrehozása a bérlői szinten |
> | Műveletek | Microsoft. ApiManagement/Service/policyDescriptions/READ | Felsorolja az összes házirend leírását. |
> | Műveletek | Microsoft.ApiManagement/service/policySnippets/read | Felsorolja az összes házirend-kódrészletet. |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/read | A portál bejelentkezési beállításainak beszerzése, illetve a portálra vonatkozó regisztrációs beállítások beszerzése, illetve a portál delegálási beállításainak beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/write | A bejelentkezési beállítások frissítése. vagy a bejelentkezési beállítások létrehozása vagy frissítése. vagy frissítse a regisztrációs beállításokat, vagy frissítse a regisztrációs beállításokat, vagy frissítse a delegálási beállításokat. vagy a delegálási beállítások létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/delete | A megadott termékből törli a megadott API-t. |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/read | A termékhez társított API-k gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/write | Egy API-t ad hozzá a megadott termékhez. |
> | Műveletek | Microsoft.ApiManagement/service/products/delete | Termék törlése. |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/delete | Törli a megadott csoport és termék közötti társítást. |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/read | Felsorolja a megadott termékhez társított fejlesztői csoportok gyűjteményét. |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/write | Hozzáadja a megadott fejlesztői csoport társítását a megadott termékhez. |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/delete | Törli a termék házirend-konfigurációját. |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/read | A szabályzat konfigurációjának beolvasása a termék szintjén. vagy szerezze be a szabályzat konfigurációját a termék szintjén. |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/write | Létrehozza vagy frissíti a termékhez tartozó házirend-konfigurációt. |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/delete | A házirend-konfiguráció törlése a termék szintjén |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/read | A szabályzat konfigurációjának beolvasása a termék szintjén |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/write | Házirend-konfiguráció létrehozása a termék szintjén |
> | Műveletek | Microsoft.ApiManagement/service/products/read | Felsorolja a megadott szolgáltatási példányban található termékek gyűjteményét. vagy lekéri az azonosító alapján megadott termék részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/products/subscriptions/read | Felsorolja a megadott termékre vonatkozó előfizetések gyűjteményét. |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/delete | Válassza le a címkét a termékből. |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/read | Felsorolja a termékhez társított összes címkét. vagy a termékhez társított címke beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/write | Címke kiosztása a termékhez. |
> | Műveletek | Microsoft.ApiManagement/service/products/write | Létrehoz vagy frissít egy terméket. vagy frissítse a meglévő termék részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/productsByTags/read | A címkékhez társított termékek gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/properties/delete | A API Management szolgáltatás példányának adott tulajdonságát törli. |
> | Műveletek | Microsoft.ApiManagement/service/properties/read | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott tulajdonság részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/properties/write | Létrehoz vagy frissít egy tulajdonságot. vagy frissíti az adott tulajdonságot. |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/read | Kvóta számláló értékének lekérése az időszakhoz |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/write | Kvóta számlálójának jelenlegi értékének beállítása |
> | Műveletek | Microsoft.ApiManagement/service/quotas/read | Kvóta értékének beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/quotas/write | Kvóta számlálójának jelenlegi értékének beállítása |
> | Műveletek | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Műveletek | Microsoft.ApiManagement/service/regions/read | Felsorolja az összes olyan Azure-régiót, amelyben a szolgáltatás létezik. |
> | Műveletek | Microsoft.ApiManagement/service/reports/read | Jelentések beolvasása időszakok szerint összesítve, vagy a jelentés beolvasása földrajzi régió szerint összesítve vagy a fejlesztők által összesített jelentés beolvasása.<br>vagy a jelentések összesítése termékek alapján.<br>vagy beolvashatja az API-k által összesített jelentést, vagy beolvashatja az Operations által összesített jelentést, vagy az előfizetés alapján összesítheti a jelentést.<br>vagy kérések beolvasása az adatjelentésekről |
> | Műveletek | Microsoft.ApiManagement/service/restore/action | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/delete | Törli a megadott előfizetést. |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/read | Az API Management Service-példány összes előfizetését listázza. vagy lekéri a megadott előfizetési entitást. |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Újragenerálta a API Management szolgáltatási példány meglévő előfizetésének elsődleges kulcsát. |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Újragenerálja a API Management szolgáltatási példány meglévő előfizetésének másodlagos kulcsát. |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/write | Létrehozza vagy frissíti a megadott felhasználó előfizetését a megadott termékre. vagy frissíti az azonosító alapján megadott előfizetés részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/tagResources/read | A címkékhez társított erőforrások gyűjteményét listázza. |
> | Műveletek | Microsoft.ApiManagement/service/tags/delete | Törli a API Management szolgáltatási példány adott címkéjét. |
> | Műveletek | Microsoft.ApiManagement/service/tags/read | A szolgáltatási példányon belül definiált címkék gyűjteményét listázza. vagy lekéri az azonosító alapján megadott címke részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/tags/write | Létrehoz egy címkét. vagy frissíti az azonosító alapján megadott címke részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/templates/delete | Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása |
> | Műveletek | Microsoft.ApiManagement/service/templates/read | Az összes e-mail-sablon beolvasása vagy API Management e-mail sablon adatainak beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/templates/write | API Management e-mail-sablon létrehozása vagy frissítése API Management e-mail-sablon frissítése |
> | Műveletek | Microsoft.ApiManagement/service/tenant/delete | A bérlő házirend-konfigurációjának eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/deploy/action | Egy központi telepítési feladat futtatásával alkalmazza a megadott git-ág módosításait az adatbázisban található konfigurációra. |
> | Műveletek | Microsoft.ApiManagement/service/tenant/operationResults/read | A művelet eredményeinek beolvasása vagy egy adott művelet eredményének beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/read | Az API Management szolgáltatás globális szabályzat-definíciójának beolvasása. vagy a bérlői hozzáférési információk részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Elsődleges elérési kulcs előállítása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Másodlagos elérési kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/save/action | Elkészíti a commit és a konfiguráció pillanatképét a tárházban lévő megadott ágra |
> | Műveletek | Microsoft.ApiManagement/service/tenant/syncState/read | Az utolsó git-szinkronizálás állapotának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/validate/action | Ellenőrzi a megadott git-ág változásait. |
> | Műveletek | Microsoft.ApiManagement/service/tenant/write | Házirend-konfiguráció beállítása a bérlőhöz vagy a bérlői hozzáférési információk frissítése – részletek |
> | Műveletek | Microsoft.ApiManagement/service/updatecertificate/action | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Műveletek | Microsoft.ApiManagement/service/updatehostname/action | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/users/action | Új felhasználó regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/users/confirmations/send/action | Visszaigazolás küldése |
> | Műveletek | Microsoft.ApiManagement/service/users/delete | Adott felhasználó törlése. |
> | Műveletek | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Egy hitelesítési jogkivonatot tartalmazó átirányítási URL-címet kér le egy adott felhasználó a fejlesztői portálon való aláírásához. |
> | Műveletek | Microsoft.ApiManagement/service/users/groups/read | Felsorolja az összes felhasználói csoportot. |
> | Műveletek | Microsoft.ApiManagement/service/users/identities/read | Az összes felhasználói identitás listája. |
> | Műveletek | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/read | Felsorolja a megadott szolgáltatási példányban regisztrált felhasználók gyűjteményét. vagy lekéri a felhasználó azonosítójában megadott adatokat. |
> | Műveletek | Microsoft.ApiManagement/service/users/subscriptions/read | Felsorolja a megadott felhasználó előfizetéseit. |
> | Műveletek | Microsoft.ApiManagement/service/users/token/action | A felhasználó megosztott hozzáférési engedélyezési jogkivonatának beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/users/write | Létrehoz vagy frissít egy felhasználót. vagy frissíti az azonosító alapján megadott felhasználó részleteit. |
> | Műveletek | Microsoft.ApiManagement/service/write | API Management szolgáltatás új példányának létrehozása |
> | Műveletek | Microsoft.ApiManagement/unregister/action | A Microsoft. ApiManagement erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Authorization/classicAdministrators/delete | Eltávolítja a rendszergazdát az előfizetésből. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Az előfizetés rendszergazdai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/read | Az előfizetés rendszergazdáinak beolvasása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/write | Egy előfizetéshez tartozó rendszergazda hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Authorization/denyAssignments/delete | Egy megtagadási hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/denyAssignments/olvasás | Megtagadási hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/denyAssignments/írás | Megtagadási hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/elevateAccess/művelet | A hívó felhasználói hozzáférés rendszergazdai hozzáférésének engedélyezése a bérlői hatókörben |
> | Műveletek | Microsoft.Authorization/locks/delete | Zárolások törlése a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/locks/read | Lekéri a megadott hatókör zárolásait. |
> | Műveletek | Microsoft. Authorization/zárolás/írás | Zárolások hozzáadása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/operations/read | A műveletek listájának beolvasása |
> | Műveletek | Microsoft. Authorization/engedélyek/olvasás | Felsorolja az összes olyan engedélyt, amelyet a hívó adott hatókörben adott meg. |
> | Műveletek | Microsoft. Authorization/szabályzatok/naplózás/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "audit" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/auditIfNotExists/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "auditIfNotExists" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/megtagadás/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "megtagadás" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/deployIfNotExists/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "deployIfNotExists" hatással |
> | Műveletek | Microsoft.Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/policyAssignments/olvasás | Szabályzat-hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/policyAssignments/írás | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/delete | Házirend-definíció törlése. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/read | Házirend-definíció adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/write | Hozzon létre egy egyéni szabályzat-definíciót. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/delete | Házirend-készlet definíciójának törlése. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/read | Házirend-készlet definíciójának beolvasása. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/write | Hozzon létre egy egyéni házirend-készlet definícióját. |
> | Műveletek | Microsoft.Authorization/providerOperations/read | A szerepkör-definíciókban használható összes erőforrás-szolgáltató műveleteinek beolvasása. |
> | Műveletek | Microsoft.Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/roleAssignments/read | Szerepkör-hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/read | Szerepkör-definíció adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/roleDefinitions/írás | Egyéni szerepkör-definíció létrehozása vagy frissítése megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC regisztrációs adatainak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC-kulcsok újragenerálása iránti kérés írása |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation tanúsítvány törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/getCount/action | A tanúsítványok számának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation-tanúsítvány beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/write | Létrehoz vagy frissít egy Azure Automation tanúsítvány-eszközt |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC fordításának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC fordításának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC fordításának írása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC fordításának írása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/content/read | A konfigurációs adathordozó tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC tartalmának törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/getCount/action | Beolvas egy Azure Automation DSC tartalmának számát |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC tartalmának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/írás | Egy Azure Automation DSC tartalmának írása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation-összekötő törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/getCount/action | A kapcsolatok számának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/read | Azure Automation a kapcsolatok adategységének beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/write | Létrehoz vagy frissít egy Azure Automation-összekötő eszközt |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation típusú kapcsolattípus-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation kapcsolattípus-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/write | Létrehoz egy Azure Automation kapcsolattípus-eszközt |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation hitelesítőadat-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/getCount/action | A hitelesítő adatok számának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation hitelesítőadat-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/write | Egy Azure Automation hitelesítőadat-eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/delete | Azure Automation fiók törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid Runbook-feldolgozó erőforrások törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook Worker-erőforrások beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/output/read | A feladatok kimenetének beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation feladatok beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation feladatot folytat |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Lekéri a Azure Automation runbook tartalmát a feladatok végrehajtásának időpontjában. |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállítja egy Azure Automation feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation feladatok felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation feladatütemezés törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation feladatütemezés beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | Műveletek | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/listKeys/action | Az Automation-fiók kulcsainak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/activities/read | Lekéri Azure Automation tevékenységeket |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation PowerShell-modul törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/getCount/action | Az Automation-fiókban lévő PowerShell-modulok számának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/read | Azure Automation PowerShell-modul beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/write | Egy Azure Automation PowerShell-modul létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC csomópont-konfigurációjának törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Beolvas egy Azure Automation DSC csomópont-konfigurációs tartalmát |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Egy Azure Automation DSC csomópont-konfigurációjának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Egy Azure Automation DSC csomópont-konfigurációjának írása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodecounts/read | A megadott típushoz tartozó csomópontok számának összefoglalásának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC-csomópontok törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC-csomópontok beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC-jelentések beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC-csomópontok létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation TypeFields beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 csomag törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/python2Packages/read | Azure Automation Python 2 csomag beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/python2Packages/write | Egy Azure Automation Python 2 csomag létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 csomag törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/python3Packages/read | Azure Automation Python 3 csomag beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/python3Packages/write | Egy Azure Automation Python 3 csomag létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/read | Azure Automation fiók beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/content/read | Egy Azure Automation runbook tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation runbook törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Egy Azure Automation runbook-Piszkozat tartalmának létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Beolvassa Azure Automation runbook-Piszkozat műveleti eredményeit |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation runbook-tervezet beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Beolvas egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Egy Azure Automation runbook-Piszkozat tesztelési feladatot folytat |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Leállít egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Egy Azure Automation runbook-Piszkozat tesztelési feladata felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Létrehoz egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Módosítások visszavonása egy Azure Automation runbook-piszkozatra |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Lekéri Azure Automation runbookok számát |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation runbook-Piszkozat közzététele |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation runbook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/write | Létrehoz vagy frissít egy Azure Automation runbook |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation ütemezett eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/getCount/action | Beolvassa Azure Automation ütemtervek számát |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation ütemezett eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/write | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/READ | Egy Azure Automation szoftverfrissítési konfigurációs gép futtatásának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/READ | Azure Automation szoftverfrissítés-konfiguráció futtatásának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation szoftverfrissítés konfigurációjának törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation szoftverfrissítés konfigurációjának törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation szoftverfrissítés konfigurációjának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation szoftverfrissítés konfigurációjának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation szoftverfrissítési konfiguráció létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation szoftverfrissítési konfiguráció létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation statisztikájának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation frissítési üzembe helyezési gép beszerzése |
> | Műveletek | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Beolvas egy Azure Automation Update Management-javítási feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/usages/read | Lekéri Azure Automation használatát |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation változó eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/read | Egy Azure Automation változó eszköz beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/változók/írás | Egy Azure Automation változó eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher-feladatok törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/read | Beolvas egy Azure Automation figyelő feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher-feladatok indítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher-feladatok leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/streams/read | Beolvas egy Azure Automation megfigyelői feladatok streamjét |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation figyelőkkel kapcsolatos feladatok törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation figyelőkkel kapcsolatos feladatok beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation figyelőkkel kapcsolatos feladatok létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/write | Létrehoz egy Azure Automation figyelő feladatot |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/action | URI generálása egy Azure Automation webhookhoz |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation webhook törlése  |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/read | Egy Azure Automation webhook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/write | Egy Azure Automation webhook létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/automationAccounts/write | Egy Azure Automation fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/operations/read | A Azure Automation erőforrásaihoz elérhető műveletek beolvasása |
> | Műveletek | Microsoft.Automation/register/action | Az előfizetés regisztrálása Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C-címtár erőforrásának törlése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C-címtár erőforrásának megtekintése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-címtár erőforrásának létrehozása vagy frissítése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2ctenants/read | Felsorolja az összes B2C-bérlőt, ahol a felhasználó tagja |
> | Műveletek | Microsoft.AzureActiveDirectory/operations/read | A Microsoft. AzureActiveDirectory erőforrás-szolgáltatóhoz elérhető összes API-művelet olvasása |
> | Műveletek | Microsoft.AzureActiveDirectory/register/action | Előfizetés regisztrálása a Microsoft. AzureActiveDirectory erőforrás-szolgáltatónál |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureStack/Operations/read | Erőforrás-szolgáltatói művelet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/register/action | Előfizetés regisztrálása a Microsoft. AzureStack erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack ügyfél-előfizetés törlése |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/read | Lekéri egy Azure Stack ügyfél-előfizetés tulajdonságait |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/write | Létrehoz vagy frissít egy Azure Stack ügyfél-előfizetést |
> | Műveletek | Microsoft.AzureStack/registrations/delete | Azure Stack Regisztráció törlése |
> | Műveletek | Microsoft.AzureStack/registrations/getActivationKey/action | A legújabb Azure Stack aktiválási kulcs beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace-termék részletes adatainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/products/read | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/uploadProductLog/művelet | A Piactéri termék műveleti állapotának és időbélyegének rögzítése Azure Stack |
> | Műveletek | Microsoft.AzureStack/registrations/read | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/write | Létrehoz vagy frissít egy Azure Stack regisztrációt |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/delete | Egy alkalmazás törlése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/olvasás | Alkalmazások felsorolása vagy egy alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/aktiválás/művelet | Alkalmazás-csomag aktiválása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/delete | Alkalmazáscsomag törlése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/olvasás | Egy alkalmazáscsomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/írás | Új alkalmazáscsomag létrehozása vagy meglévő alkalmazáscsomag frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/írás | Új alkalmazás létrehozása vagy meglévő alkalmazás frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/certificateOperationResults/olvasás | Egy hosszú ideig futó tanúsítvány-művelet eredményének beolvasása batch-fiókon |
> | Műveletek | Microsoft. batch/batchAccounts/Certificates/cancelDelete/Action | Egy batch-fiókban lévő tanúsítvány sikertelen törlésének megszakítása |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/törlés | Tanúsítvány törlése batch-fiókból |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/olvasás | A Batch-fiókban lévő tanúsítványok listája vagy a tanúsítvány tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/írás | Új tanúsítvány létrehozása batch-fiókon vagy meglévő tanúsítvány frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/delete | Batch-fiók törlése |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/törlés | Feladatot töröl egy batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/olvasás | A Batch-fiókban lévő feladatok felsorolása vagy a feladat tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/írás | Új feladatot hoz létre egy batch-fiókon, vagy frissít egy meglévő feladatot. |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/delete | Ütemezett feladatütemezés törlése batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/olvasás | Egy batch-fiókban lévő feladatok ütemezett listáját vagy egy feladatütemezés tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/írás | Új feladatütemezés létrehozása batch-fiókon vagy meglévő feladatütemezés frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/listkeys műveletének beolvasása/művelet | Egy batch-fiók hozzáférési kulcsainak listázása |
> | Műveletek | Microsoft. batch/batchAccounts/operationResults/olvasás | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/poolOperationResults/olvasás | Egy hosszú ideig futó készlet műveletének eredményét jeleníti meg egy batch-fiókban |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/törlés | Készlet törlése batch-fiókból |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/disableAutoscale/művelet | Letiltja a Batch-fiókok automatikus méretezését |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/olvasás | A Batch-fiókban lévő készletek felsorolása vagy a készlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/stopResize/művelet | Egy folyamatban lévő átméretezési művelet leállítása egy batch-fiók készletén |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/írás | Létrehoz egy új készletet egy batch-fiókon, vagy frissít egy meglévő készletet. |
> | Műveletek | Microsoft. batch/batchAccounts/olvasás | Batch-fiókokat listáz, vagy egy batch-fiók tulajdonságait kéri le. |
> | Műveletek | Microsoft. batch/batchAccounts/regeneratekeys/művelet | Egy batch-fiók hozzáférési kulcsainak újragenerálása |
> | Műveletek | Microsoft. batch/batchAccounts/syncAutoStorageKeys/művelet | A Batch-fiókhoz konfigurált automatikus Storage-fiókhoz tartozó hozzáférési kulcsok szinkronizálása |
> | Műveletek | Microsoft. batch/batchAccounts/írás | Új batch-fiók létrehozása vagy meglévő batch-fiók frissítése |
> | Műveletek | Microsoft. batch/Locations/accountOperationResults/READ | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Műveletek | Microsoft. batch/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. batch/helyszínek/kvóták/olvasás | A megadott előfizetéshez tartozó Batch-kvóták beolvasása a megadott Azure-régióban |
> | Műveletek | Microsoft. batch/Operations/READ | A Microsoft. batch erőforrás-szolgáltatón elérhető műveletek listája |
> | Műveletek | Microsoft. batch/regisztráció/művelet | Regisztrálja az előfizetést a Batch erőforrás-szolgáltató számára, és lehetővé teszi a Batch-fiókok létrehozását. |
> | Műveletek | Microsoft. batch/regisztráció/művelet | A Batch erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése a Batch-fiókok létrehozásának megakadályozása érdekében |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Billing/billingAccounts/szerződések/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/ügyfelek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/számlák/árlista/Letöltés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/áthelyezés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/átvitel/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/termékek/átvitel/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/árlista/Letöltés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/írás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/írás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingSubscriptions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/Customers/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/ügyfelek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/részlegek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/enrollmentAccounts/billingPermissions/READ |  |
> | Műveletek | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Műveletek | Microsoft. Billing/billingAccounts/enrollmentDepartments/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/Products/READ |  |
> | Műveletek | Microsoft. számlázás/billingAccounts/olvasás |  |
> | Műveletek | Microsoft. számlázás/billingAccounts/írás |  |
> | Műveletek | Microsoft. Billing/részlegek/olvasás |  |
> | Műveletek | Microsoft. számlázás/regisztráció/művelet |  |
> | Műveletek | Microsoft. Billing/validateAddress/művelet |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BingMaps/mapApis/Delete | Művelet törlése |
> | Műveletek | Microsoft.BingMaps/mapApis/listSecrets/action | A titkok listázása |
> | Műveletek | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Az erőforráshoz tartozó egyszeri bejelentkezés engedélyezési jogkivonatának olvasása |
> | Műveletek | Microsoft.BingMaps/mapApis/Read | Olvasási művelet |
> | Műveletek | Microsoft.BingMaps/mapApis/regenerateKey/action | Újragenerálta a kulcsot |
> | Műveletek | Microsoft.BingMaps/mapApis/Write | Írási művelet |
> | Műveletek | Microsoft.BingMaps/Operations/read | A művelet leírása. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/delete | Egy meglévő Blockchain-tag törlése. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/listApiKeys/művelet | Lekérdezi vagy felsorolja a meglévő Blockchain-tag API-kulcsait. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag (oka) t. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | Műveletek | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Töröl egy meglévő Blockchain-tag tranzakciós csomópontot. |
> | Műveletek | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Lekérdezi vagy felsorolja a meglévő Blockchain-tagok tranzakciós csomópontjának API-kulcsait. |
> | Műveletek | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | Műveletek | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Létrehoz vagy frissít egy Blockchain-tag tranzakciós csomópontot. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/Write | Egy Blockchain-tag létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Lekéri a Blockchain tagjainak működési eredményeit. |
> | Műveletek | Microsoft. Blockchain/Locations/checkNameAvailability/Action | Ellenőrzi, hogy az erőforrás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. Blockchain/Operations/READ | A Microsoft Blockchain erőforrás-szolgáltató összes műveletének listázása. |
> | Műveletek | Microsoft. Blockchain/regisztráció/művelet | Regisztrálja az előfizetést a Blockchain erőforrás-szolgáltatóhoz. |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/assignmentOperations/READ | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/delete | Bármilyen tervrajz-összetevő törlése |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/READ | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Azure-tervrajzok egyszerű objektum-azonosítójának beolvasása. |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/Write | Tervrajzok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/törlés | Bármilyen tervrajz-összetevő törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/olvasás | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/írás | Tervrajzok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/törlés | Bármilyen tervrajz törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/olvasás | Bármilyen tervrajz beolvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/összetevők/olvasás | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/törlés | Bármilyen tervrajz törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/olvasás | Bármilyen tervrajz beolvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/írás | Bármilyen tervrajz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/írás | Bármilyen tervrajz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/regisztráció/művelet | Regisztrálja az Azure-tervrajzok erőforrás-szolgáltatóját |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. BotService/botServices/csatornák/törlés | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/csatornák/olvasás | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/csatornák/írás | Bot-szolgáltatás írása |
> | Műveletek | Microsoft. BotService/botServices/Connections/delete | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/Connections/READ | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/Connections/Write | Bot-szolgáltatás írása |
> | Műveletek | Microsoft.BotService/botServices/delete | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/READ | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/Write | Bot-szolgáltatás írása |
> | Műveletek | Microsoft. BotService/Locations/operationresults/READ | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. BotService/Operations/READ | Az összes erőforrástípus műveleteinek beolvasása |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Cache/checknameavailability/action | Ellenőrzi, hogy a név elérhető-e új Redis Cache |
> | Műveletek | Microsoft.Cache/locations/operationresults/read | Egy hosszú ideig futó művelet eredményének beolvasása, amelyre a "location" fejlécet korábban visszaadották az ügyfélnek |
> | Műveletek | Microsoft.Cache/operations/read | A "Microsoft. cache" szolgáltató által támogatott műveletek felsorolása. |
> | Műveletek | Microsoft.Cache/redis/delete | A teljes Redis Cache törlése |
> | Műveletek | Microsoft.Cache/redis/export/action | Redis-adatforrások exportálása előre rögzített tárolási blobokra megadott formátumban |
> | Műveletek | Microsoft.Cache/redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabály-szabályainak törlése |
> | Műveletek | Microsoft.Cache/redis/firewallRules/read | Egy Redis Cache IP-tűzfalszabályok beszerzése |
> | Műveletek | Microsoft.Cache/redis/firewallRules/write | Egy Redis Cache IP-tűzfalszabály-szabályainak szerkesztése |
> | Műveletek | Microsoft.Cache/redis/forceReboot/action | Kényszerítse újra a gyorsítótár-példány újraindítását, ami adatvesztéssel jár. |
> | Műveletek | Microsoft.Cache/redis/import/action | Megadott formátumú adatok importálása több blobból a Redis-be |
> | Műveletek | Microsoft.Cache/redis/linkedservers/delete | Csatolt kiszolgáló törlése Redis Cache |
> | Műveletek | Microsoft.Cache/redis/linkedservers/read | Redis-gyorsítótárhoz társított csatolt kiszolgálók beolvasása. |
> | Műveletek | Microsoft.Cache/redis/linkedservers/write | Csatolt kiszolgáló hozzáadása Redis Cachehoz |
> | Műveletek | Microsoft.Cache/redis/listKeys/action | Redis Cache hozzáférési kulcsok értékének megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/listUpgradeNotifications/read | A gyorsítótár-bérlő legújabb frissítési értesítéseinek listázása. |
> | Műveletek | Microsoft.Cache/redis/metricDefinitions/read | Egy Redis Cache elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/delete | Redis Cache javítási ütemtervének törlése |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/read | Egy Redis Cache javítási ütemtervének beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/write | Redis Cache javítási ütemtervének módosítása |
> | Műveletek | Microsoft.Cache/redis/read | A Redis Cache beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/regenerateKey/action | Redis Cache hozzáférési kulcsok értékének módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/start/action | Indítsa el a gyorsítótár-példányt. |
> | Műveletek | Microsoft.Cache/redis/stop/action | Állítsa le a gyorsítótár-példányt. |
> | Műveletek | Microsoft.Cache/redis/write | A Redis Cache beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/register/action | Regisztrálja a Microsoft. cache erőforrás-szolgáltatót egy előfizetéssel |
> | Műveletek | Microsoft.Cache/unregister/action | A Microsoft. cache erőforrás-szolgáltató regisztrációjának törlése előfizetéssel |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Capacity/appliedreservations/read | Összes foglalás olvasása |
> | Műveletek | Microsoft.Capacity/calculateexchange/action | Kiszámítja az új vásárlások árfolyamát és árát, és visszaadja a házirend-hibákat. |
> | Műveletek | Microsoft. Capacity/calculateprice/művelet | A foglalás árának kiszámítása |
> | Műveletek | Microsoft.Capacity/catalogs/read | Foglalási katalógus olvasása |
> | Műveletek | Microsoft. Capacity/checkoffers/művelet | Előfizetési ajánlatok keresése |
> | Műveletek | Microsoft.Capacity/checkscopes/action | Előfizetés keresése |
> | Műveletek | Microsoft. Capacity/commercialreservationorders/olvasás | Bármely Bérlőben létrehozott foglalási megrendelések beolvasása |
> | Műveletek | Microsoft.Capacity/exchange/action | Bármilyen foglalás cseréje |
> | Műveletek | Microsoft.Capacity/operations/read | Bármilyen művelet beolvasása |
> | Műveletek | Microsoft.Capacity/register/action | Regisztrálja a kapacitás erőforrás-szolgáltatót, és lehetővé teszi a kapacitás-erőforrások létrehozását. |
> | Műveletek | Microsoft.Capacity/reservationorders/action | Bármely foglalás frissítése |
> | Műveletek | Microsoft.Capacity/reservationorders/availablescopes/action | Bármely elérhető hatókör megkeresése |
> | Műveletek | Microsoft.Capacity/reservationorders/calculaterefund/action | Kiszámítja a visszatérítés összegét és az új vásárlás árát, és visszaadja a házirend-hibákat. |
> | Műveletek | Microsoft.Capacity/reservationorders/delete | Foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/merge/action | Bármilyen foglalás egyesítése |
> | Műveletek | Microsoft.Capacity/reservationorders/read | Összes foglalás olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/action | Bármely foglalás frissítése |
> | Műveletek | Microsoft. Capacity/reservationorders/Reservations/availablescopes/Action | Bármely elérhető hatókör megkeresése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/delete | Foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/read | Összes foglalás olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/revisions/read | Összes foglalás olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/write | Foglalás létrehozása |
> | Műveletek | Microsoft.Capacity/reservationorders/return/action | Bármilyen foglalás visszaküldése |
> | Műveletek | Microsoft.Capacity/reservationorders/split/action | Foglalás felosztása |
> | Műveletek | Microsoft.Capacity/reservationorders/swap/action | Bármilyen foglalás cseréje |
> | Műveletek | Microsoft.Capacity/reservationorders/write | Foglalás létrehozása |
> | Műveletek | Microsoft.Capacity/tenants/register/action | Bármely bérlő regisztrálása |
> | Műveletek | Microsoft.Capacity/unregister/action | Bérlő regisztrációjának törlése |
> | Műveletek | Microsoft.Capacity/validatereservationorder/action | Bármely foglalás ellenőrzése |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. CDN/CheckNameAvailability/művelet |  |
> | Műveletek | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/edgenodes/delete |  |
> | Műveletek | Microsoft.Cdn/edgenodes/read |  |
> | Műveletek | Microsoft. CDN/edgenodes/írás |  |
> | Műveletek | Microsoft.Cdn/operationresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/write |  |
> | Műveletek | Microsoft.Cdn/operations/read |  |
> | Műveletek | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/profiles/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/write |  |
> | Műveletek | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Műveletek | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Műveletek | Microsoft. CDN/profilok/olvasás |  |
> | Műveletek | Microsoft. CDN/profilok/írás |  |
> | Műveletek | Microsoft.Cdn/register/action | Regisztrálja az előfizetést a CDN erőforrás-szolgáltató számára, és lehetővé teszi a CDN-profilok létrehozását. |
> | Műveletek | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Meglévő tanúsítvány törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | A tanúsítványok listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Új tanúsítvány hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Delete | Meglévő AppServiceCertificate törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Read | A Tanúsítványrendelések listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Meglévő tanúsítványrendelési újrakibocsátása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Meglévő tanúsítványrendelési megújítása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Tanúsítvány e-mail újraküldése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Kérelem e-mail-címének újraküldése egy másik e-mail-címre |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hely zárolásának beolvasása egy kiállított App Service-tanúsítvány |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | A tanúsítványkérelmek listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Tanúsítvány e-mail-előzményeinek beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Tartomány tulajdonjogának ellenőrzése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Write | Új Tanúsítványrendelési hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/operations/Read | Az App Service-tanúsítvány regisztrációjának összes műveletének listázása |
> | Műveletek | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Egyszerű szolgáltatásnév kiépítése a Service app számára |
> | Műveletek | Microsoft.CertificateRegistration/register/action | Regisztrálja az előfizetéshez tartozó Microsoft-tanúsítványok erőforrás-szolgáltatóját |
> | Műveletek | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Tanúsítvány-megvásárlási objektum ellenőrzése az elküldése nélkül |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicCompute/capabilities/read | Megjeleníti a képességeket |
> | Műveletek | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Ellenőrzi egy adott tartománynév rendelkezésre állását. |
> | Műveletek | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Egy adott tartománynév elérhetőségének beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/active/write | Beállítja az aktív tartománynevet. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/capabilities/read | A tartománynév funkcióinak megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/domainNames/delete | Távolítsa el az erőforrások tartománynevét. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Megjeleníti a telepítési tárolóhelyeket. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Szerepkör beszerzése a tartománynév üzembe helyezési pontján |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Szerepkör-példány beszerzése a szerepkörhöz a tartománynév üzembe helyezési tárolóhelyén |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Az üzembe helyezési pont állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adja hozzá az üzembe helyezési pont állapotát. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Frissítési tartomány beolvasása az üzembe helyezési ponthoz a tartománynév alapján |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Frissítési tartomány frissítése a tartománynév üzembe helyezési nyílásába |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/delete | Távolítsa el a tartománynevek kiterjesztéseit. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/read | A tartománynév-kiterjesztéseket adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/write | Adja hozzá a tartománynevek kiterjesztéseit. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Új belső terheléselosztási egyenleg eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Beolvassa a tartománynevek belső terheléselosztó műveleti állapotát. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | A belső terheléselosztó beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Létrehoz egy új belső terheléselosztást. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Beolvassa a tartománynevek elosztott terhelésű végpontjának műveleti állapotát. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Az elosztott terhelésű végpontok beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adja hozzá a elosztott terhelésű végpont készletet. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/operationstatuses/read | A tartománynév műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/read | Erőforrások tartománynevének visszaadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Törölje a használt szolgáltatási tanúsítványokat. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | A tartománynevek szolgáltatási tanúsítványainak műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | A használt szolgáltatási tanúsítványokat adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adja hozzá vagy módosítsa a használt szolgáltatási tanúsítványokat. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Egy üzembe helyezési pont áttelepítését szakítja meg. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Egy üzembe helyezési pont áttelepítését véglegesíti. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/delete | Egy adott üzembe helyezési pont törlése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | A tartománynevek tárolóhelyek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Előkészíti az üzembe helyezési pont áttelepítését. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/read | Megjeleníti a telepítési tárolóhelyeket. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Távolítsa el az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Beolvassa a tartománynevek tárolóhelyek szerepkör-bővítmény hivatkozásainak műveleti állapotát. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adja hozzá vagy módosítsa az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | A tartománynévhez tartozó szerepkör-metrika definíciójának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | A tartománynév szerepkör-metrikájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | A tartománynevek tárolóhelye szerepkör műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/read | Az üzembe helyezési pont szerepkörének beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Letölti a távoli asztali kapcsolati fájlt a szerepkör-példányhoz a tartománynév tárolóhelye szerepkörben. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Beolvassa a szerepkör-példány műveleti állapotát a tartománynevek tárolóhelye szerepkörben. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | A szerepkör-példány beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Újraépíti a szerepkör-példányt. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | A szerepkör-példány rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Újraindítja a szerepkör-példányokat. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Szerepkör-SKU beszerzése az üzembe helyezési ponthoz. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/write | Szerepkör hozzáadása az üzembe helyezési ponthoz. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/start/action | Elindítja a telepítési tárolóhelyet. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Az üzembe helyezési pont állapota leállítva értékre változik. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Az üzembe helyezési pont állapotának elindítását módosítja. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/stop/action | Felfüggeszti az üzembe helyezési pontot. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Menjen a tartomány frissítéséhez. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Egy üzembe helyezési pont áttelepítésének ellenőrzése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/swap/action | Kicseréli az átmeneti tárolóhelyet az üzemi tárolóhelyre. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/write | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/moveSubscriptionResources/action | Helyezze át az összes klasszikus erőforrást egy másik előfizetésbe. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystemFamilies/read | Felsorolja a Microsoft Azureban elérhető vendég operációsrendszer-családokat, valamint felsorolja az egyes családokhoz elérhető operációsrendszer-verziókat is. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystems/read | A Microsoft Azure jelenleg elérhető vendég operációs rendszer verzióit sorolja fel. |
> | Műveletek | Microsoft.ClassicCompute/operations/read | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/register/action | Regisztrálás a klasszikus számítási feladatokba |
> | Műveletek | Microsoft.ClassicCompute/resourceTypes/skus/read | Beolvassa a támogatott erőforrástípusok SKU-listáját. |
> | Műveletek | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Érvényesítse az előfizetés rendelkezésre állását a klasszikus áthelyezési művelethez. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | A virtuális géphez társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Hozzáadja a virtuális géphez társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | A lehetséges aszinkron műveletek beolvasása |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Adatlemez csatolása egy virtuális géphez. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/capture/action | Virtuális gép rögzítése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/delete | Eltávolítja a virtuális gépeket. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Adatlemez leválasztása a virtuális gépről. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Virtuális gép diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/disks/read | Adatlemezek listájának lekérése |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Letölti a virtuális gép RDP-fájlját. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/read | Lekéri a virtuálisgép-bővítményt. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/write | A virtuálisgép-bővítményt helyezi át. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | A virtuális gép metrikai definíciójának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/metrics/read | A metrikák beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati adapterhez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | A hálózati adapterhez társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Hozzáadja a hálózati adapterhez társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | A virtuális gépek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Karbantartást végez a virtuális gépen. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/read | A virtuális gépek listájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/redeploy/action | A virtuális gép újbóli üzembe helyezése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/restart/action | Újraindítja a virtuális gépeket. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Állítsa le a virtuális gépet. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/start/action | Indítsa el a virtuális gépet. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/stop/action | Leállítja a virtuális gépet. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/READ | Expressz útvonalon keresztüli kapcsolatok állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/törlés | Express Route-kapcsolatok közötti társítás törlése |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/operationstatuses/olvasás | Expressz útvonal keresztben való összekapcsolási műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/olvasás | Expressz útvonalon keresztüli kapcsolatok összekapcsolásának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/írás | Express Route-kapcsolatok közötti társítás hozzáadása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/READ | Expressz útvonal-kereszt kapcsolatok beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Express Route-kereszt kapcsolatainak hozzáadása |
> | Műveletek | Microsoft. ClassicNetwork/gatewaySupportedDevices/READ | A támogatott eszközök listájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A hálózati biztonsági csoportok diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése, ezt a műveletet az információk erőforrás-szolgáltatója egészíti ki. |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/logDefinitions/READ | A hálózati biztonsági csoport eseményeinek beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/read | A hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | A biztonsági szabály beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Egy biztonsági szabály hozzáadására vagy frissítésére szolgál. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/write | Új hálózati biztonsági csoport hozzáadására szolgál. |
> | Műveletek | Microsoft. ClassicNetwork/Operations/READ | Klasszikus hálózati műveletek beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/kvóták/olvasás | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/regisztráció/művelet | Regisztrálás a klasszikus hálózatra |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/JOIN/Action | Csatlakozás fenntartott IP-címhez |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/link/action | Fenntartott IP-cím csatolása |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/operationStatuses/READ | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/Write | Új fenntartott IP-cím hozzáadása |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/művelet | Megszakítja egy Virtual Network áttelepítését |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/képességek/olvasás | Megjeleníti a képességeket |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Egy adott IP-cím rendelkezésre állását ellenőrzi egy virtuális hálózaton. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/művelet | Egy Virtual Network áttelepítését véglegesíti |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/delete | Törli a virtuális hálózatot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/delete | Ügyféltanúsítvány visszavonásának megszüntetése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/READ | A visszavont Ügyféltanúsítványok beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/Write | Ügyféltanúsítvány visszavonása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/delete | Törli a virtuális hálózati átjáró ügyféltanúsítványt. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Letöltés/művelet | Tanúsítvány letöltése ujjlenyomat alapján. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/listPackage/művelet | Felsorolja a virtuális hálózati átjáró tanúsítványainak csomagját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/READ | Keresse meg az ügyfél főtanúsítványait. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/Write | Feltölt egy új ügyfél főtanúsítványát. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/csatlakozás/művelet | Egy hely és hely közötti átjáró kapcsolatának csatlakoztatása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/kapcsolat bontása/művelet | A hely és a hely közötti átjáró kapcsolatának leválasztása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/olvasás | A kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/teszt/művelet | Helyek közötti átjáró kapcsolatának tesztelése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/törlés | Törli a virtuális hálózati átjárót. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDeviceConfigurationScript/művelet | Letölti az eszköz konfigurációs parancsfájlját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDiagnostics/művelet | Az átjáró-diagnosztika letöltése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listCircuitServiceKey/művelet | Az áramköri szolgáltatás kulcsának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listPackage/művelet | Felsorolja a virtuális hálózati átjáró csomagját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/operationStatuses/READ | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/csomagok/olvasás | A virtuális hálózati átjáró csomagjának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/olvasás | Lekéri a virtuális hálózati átjárókat. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/startDiagnostics/művelet | Elindítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/stopDiagnostics/művelet | Leállítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/írás | Egy virtuális hálózati átjáró hozzáadására szolgál. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozik a virtuális hálózathoz. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/társ/művelet | A virtuális hálózatot egy másik virtuális hálózattal társítja. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/művelet | Egy Virtual Network áttelepítésének előkészítése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/read | Szerezze be a virtuális hálózatot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Törli a távoli virtuális hálózat társ-felügyeleti proxyját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/READ | Lekéri a távoli virtuális hálózati társ-felügyeleti proxyt. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | Hozzáadja vagy frissíti a távoli virtuális hálózat társ-felügyeleti proxyját. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális hálózati alhálózat társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Az alhálózathoz társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Hozzáadja az alhálózathoz társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/művelet | Egy Virtual Network áttelepítésének ellenőrzése |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/READ | A virtuális hálózat társításának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicStorage/capabilities/read | Megjeleníti a képességeket |
> | Műveletek | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | A Storage-fiók rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | A Storage-fiók rendelkezésre állásának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/disks/read | A Storage-fiók lemezét adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/images/operationstatuses/read | Beolvassa a rendszerkép műveleti állapotát. |
> | Műveletek | Microsoft.ClassicStorage/images/read | A képet adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/operations/read | Klasszikus tárolási műveletek beolvasása |
> | Műveletek | Microsoft.ClassicStorage/osImages/read | Az operációs rendszer rendszerképét adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/osPlatformImages/read | Lekéri az operációs rendszer platformjának rendszerképét. |
> | Műveletek | Microsoft.ClassicStorage/publicImages/read | A nyilvános virtuális gép rendszerképének beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/register/action | Regisztrálás a klasszikus tárolóba |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Megszakítja egy Storage-fiók áttelepítését. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/commitMigration/művelet | Egy Storage-fiók áttelepítését véglegesíti. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/delete | Törölje a Storage-fiókot. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/delete | Egy adott Storage-fiók lemezének törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/read | A Storage-fiók lemezét adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/write | Egy Storage-fiók lemezét adja meg. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/delete | Egy adott Storage-fiók rendszerképének törlése. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | A Storage-fiók rendszerképének műveleti állapotát adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/read | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Egy adott tárolási fiók operációs rendszerének rendszerképét törli. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/read | A Storage-fiók operációs rendszerének rendszerképét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Egy adott Storage-fiók operációs rendszerének rendszerképét adja meg. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | A Storage-fiók áttelepítésének előkészítése. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/mérőszámok/olvasás | A metrikák beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/READ | Szerezze be az elérhető szolgáltatásokat. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | A Storage-fiók áttelepítésének ellenőrzése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Egy adott virtuálisgép-rendszerkép törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Egy adott virtuálisgép-rendszerkép műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/read | A virtuális gép rendszerképét adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Egy adott virtuálisgép-rendszerkép hozzáadására szolgál. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Write | Felvesz egy új Storage-fiókot. |
> | Műveletek | Microsoft.ClassicStorage/vmImages/read | A virtuálisgép-lemezképek felsorolása. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft. CognitiveServices/fiókok/automatikus javaslat/keresés/művelet | Ez a művelet javaslatokat nyújt egy adott lekérdezéshez vagy részleges lekérdezéshez. |
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
> | Műveletek | Microsoft.CognitiveServices/accounts/delete | API-fiókok törlése |
> | DataAction | Microsoft. CognitiveServices/accounts/EntitySearch/Search/Action | Az entitások és a helyek eredményeinek beolvasása egy adott lekérdezés esetében. |
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
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/details/Action | Egy képpel kapcsolatos elemzéseket ad vissza, például a képet tartalmazó weblapokat. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Adott lekérdezéshez kapcsolódó rendszerképek beolvasása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ImageSearch/trendek/művelet | Aktuálisan növekvő rendszerképek beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Egy magával ragadó olvasó munkamenetet hoz létre |
> | DataAction | Microsoft. CognitiveServices/fiókok/InkRecognizer/felismerés/művelet | A stroke-adathalmazok egy készlete elemzi a tartalmat, és létrehoz egy felismert entitások listáját, beleértve a felismert szöveget is. |
> | Műveletek | Microsoft.CognitiveServices/accounts/listKeys/action | Kulcsok listázása |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Lekéri a megadott lekérdezés közzétett végpontjának előrejelzését. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Egy megadott kategóriára vonatkozó híreket ad vissza. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Az adott lekérdezésre vonatkozó Hírek beszerzése. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/trendingtopics/Action | A Bing által azonosított trendekkel kapcsolatos témakörök beolvasása. Ugyanezek a témakörök a Bing kezdőlapjának alján látható szalagcímben jelennek meg. |
> | Műveletek | Microsoft.CognitiveServices/accounts/read | API-fiókok olvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/regenerateKey/action | Kulcs újragenerálta |
> | Műveletek | Microsoft.CognitiveServices/accounts/skus/read | Meglévő erőforráshoz tartozó elérhető SKU-ket olvas. |
> | DataAction | Microsoft. CognitiveServices/fiókok/helyesírás/helyesírás/művelet | Helyesírás-ellenőrzési lekérdezés eredményének beolvasása a GET vagy a POST használatával. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Az API az ismert entitások és az általános névvel ellátott entitások (\"személy\", \"hely\", \"szervezet\" stb.) listáját adja vissza egy adott dokumentumban. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Az API a bemeneti szövegben található fő beszédtémákat jelző sztringeket jeleníti meg. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Az API a felderített nyelvet és az adott 0 és 1 közötti numerikus pontszámot jeleníti meg. Az 1-hez közeli pontszámok 100%-os bizonyossággal jelzik az azonosított nyelv valódiságát. Összesen 120 nyelv támogatott. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Az API 0 és 1 közötti numerikus pontszámokat jelenít meg.<br>Az 1. ponthoz közeledő pontszámok pozitív véleményt jeleznek, míg a 0 érték a negatív hangulatot jelzi.<br>A 0,5-es pontszám jelzi az érzelmek hiányát (például<br>egy factoid-utasítás). |
> | Műveletek | Microsoft. CognitiveServices/fiókok/használati adatok/olvasás | Meglévő erőforráshoz tartozó kvóta használatának beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/details/Action | Bepillantást nyerhet egy videóra, például a kapcsolódó videókra. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Egy adott lekérdezéshez kapcsolódó videók beolvasása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/VideoSearch/trendek/művelet | Aktuálisan növekvő videók beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | A megadott képhez kapcsolódó címkék listájának beolvasása |
> | DataAction | Microsoft. CognitiveServices/fiókok/webkeresés/keresés/művelet | Egy adott lekérdezéshez webes, képképek, Hírek & videók eredményeinek beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/write | API-fiókok írása. |
> | Műveletek | Microsoft.CognitiveServices/checkDomainAvailability/action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Értesítés a Microsoft. Network VirtualNetworks vagy alhálózatok törléséről. |
> | Műveletek | Microsoft.CognitiveServices/Operations/read | Az összes elérhető művelet listázása |
> | Műveletek | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |
> | Műveletek | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Commerce/RateCard/read | Az adott előfizetéshez tartozó ajánlati adatokat, erőforrás/fogyasztásmérő metaadatait és díjszabását adja vissza. |
> | Műveletek | Microsoft.Commerce/UsageAggregates/read | Lekéri Microsoft Azure előfizetéssel való felhasználását. Az eredmény az összesített használati adatokat, az előfizetést és az erőforrással kapcsolatos információkat tartalmaz egy adott időtartományon belül. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Compute/availabilitySets/delete | A rendelkezésre állási csoport törlése |
> | Műveletek | Microsoft.Compute/availabilitySets/read | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/availabilitySets/vmSizes/read | A rendelkezésre állási csoportba tartozó virtuális gépek létrehozásához vagy frissítéséhez elérhető méretek listázása |
> | Műveletek | Microsoft.Compute/availabilitySets/write | Új rendelkezésre állási csoport létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/törlés | Lemezes titkosítási csoport törlése |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/olvasás | Lemezes titkosítási készlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/írás | Hozzon létre egy új lemez-titkosítási készletet, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft.Compute/disks/beginGetAccess/action | A lemez SAS URI-azonosítójának beolvasása a blob-hozzáféréshez |
> | Műveletek | Microsoft.Compute/disks/delete | A lemez törlése |
> | Műveletek | Microsoft.Compute/disks/endGetAccess/action | A lemez SAS URI-azonosítójának visszavonása |
> | Műveletek | Microsoft.Compute/disks/read | Lemez tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/disks/write | Új lemez létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/galleries/delete | A katalógus törlése |
> | Műveletek | Microsoft.Compute/galleries/images/delete | A katalógus rendszerképének törlése |
> | Műveletek | Microsoft.Compute/galleries/images/read | A katalógus rendszerképének tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/galleries/images/versions/delete | A katalógus rendszerkép-verziójának törlése |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/verziók/olvasás | A katalógus rendszerkép-verziójának tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/verziók/írás | Létrehoz egy új katalógus-rendszerkép verzióját, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.Compute/galleries/images/write | Létrehoz egy új katalógus-rendszerképet, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.Compute/galleries/read | A gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/írás | Új katalógus létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/hostGroups/delete | A gazda csoport törlése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/törlés | A gazdagép törlése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/olvasás | Gazdagép tulajdonságainak lekérése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/írás | Új gazdagép létrehozása vagy meglévő gazdagép frissítése |
> | Műveletek | Microsoft.Compute/hostGroups/read | Egy gazda csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/hostGroups/write | Létrehoz egy új számítógépcsoportot, vagy frissít egy meglévőt. |
> | Műveletek | Microsoft.Compute/images/delete | A rendszerkép törlése |
> | Műveletek | Microsoft.Compute/images/read | A rendszerkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/images/write | Új rendszerkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/locations/capsOperations/read | Aszinkron Caps-művelet állapotának beolvasása |
> | Műveletek | Microsoft.Compute/locations/diskOperations/read | Aszinkron lemez művelet állapotának beolvasása |
> | Műveletek | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Hozzon létre naplókat az összes kérelem időbeli intervallum szerinti megjelenítéséhez a támogatás szabályozása érdekében. |
> | Műveletek | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Hozzon létre naplókat a szabályozott kérelmek összesítésének megjelenítéséhez ResourceName, OperationName vagy az alkalmazott szabályozási házirend szerint csoportosítva. |
> | Műveletek | Microsoft.Compute/locations/operations/read | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Platform-rendszerkép ajánlat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Platform-Rendszerképbeli SKU tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Platform rendszerkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VMExtension-típus tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VMExtension-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/read | Közzétevő tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/runCommands/read | A helyen elérhető futtatási parancsok listája |
> | Műveletek | Microsoft.Compute/locations/usages/read | Lekérdezi a szolgáltatási korlátokat és az előfizetés számítási erőforrásainak aktuális használati mennyiségét egy helyen |
> | Műveletek | Microsoft.Compute/locations/vmSizes/read | A helyen elérhető virtuálisgép-méretek felsorolása |
> | Műveletek | Microsoft.Compute/operations/read | A Microsoft. számítási erőforrás-szolgáltatón elérhető műveletek felsorolása |
> | Műveletek | Microsoft.Compute/proximityPlacementGroups/delete | A közelségi elhelyezési csoport törlése |
> | Műveletek | Microsoft.Compute/proximityPlacementGroups/read | Közelségi elhelyezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/proximityPlacementGroups/write | Új közelségi elhelyezési csoport létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/register/action | Előfizetés regisztrálása a Microsoft. számítási erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.Compute/restorePointCollections/delete | Törli a visszaállítási pont gyűjteményét és a benne foglalt visszaállítási pontokat |
> | Műveletek | Microsoft.Compute/restorePointCollections/read | Egy visszaállítási pont gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/read | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI-k mellett |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/write | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Compute/restorePointCollections/write | Új visszaállítási pont gyűjtemény létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/sharedVMImages/delete | A lemezkép törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/read | Lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/delete | Célrégiókba törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/read | Célrégiókba tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/replicate/action | Célrégiókba replikálása célként megadott régiókba |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/write | Hozzon létre egy új Célrégiókba, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft.Compute/sharedVMImages/write | Új lemezkép létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/skus/read | Az előfizetéshez elérhető Microsoft. számítási SKU-EK listájának beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/beginGetAccess/action | A blob-hozzáféréshez tartozó pillanatkép SAS URI-azonosítójának beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/delete | Pillanatkép törlése |
> | Műveletek | Microsoft.Compute/snapshots/endGetAccess/action | A pillanatkép SAS URI-azonosítójának visszavonása |
> | Műveletek | Microsoft.Compute/snapshots/read | Pillanatkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/write | Új pillanatkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/unregister/action | Előfizetés regisztrációjának törlése a Microsoft. számítási erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.Compute/virtualMachines/capture/action | A virtuális merevlemezek másolásával rögzíti a virtuális gépet, és létrehoz egy sablont, amely hasonló virtuális gépek létrehozásához használható |
> | Műveletek | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Átalakítja a virtuális gép blob-alapú lemezeit a felügyelt lemezekre. |
> | Műveletek | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | Műveletek | Microsoft.Compute/virtualMachines/delete | A virtuális gép törlése |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/delete | A virtuálisgép-bővítmény törlése |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/read | Virtuálisgép-bővítmény tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/write | Új virtuálisgép-bővítményt hoz létre, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.Compute/virtualMachines/generalize/action | A virtuális gép állapotának beállítása általánosított értékre, és a virtuális gép előkészítése a rögzítéshez |
> | Műveletek | Microsoft.Compute/virtualMachines/instanceView/read | Lekéri a virtuális gép és az erőforrásainak részletes futásidejű állapotát |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | Műveletek | Microsoft.Compute/virtualMachines/performMaintenance/action | Karbantartási műveletet hajt végre a virtuális gépen. |
> | Műveletek | Microsoft.Compute/virtualMachines/powerOff/action | Kikapcsolja a virtuális gépet. Vegye figyelembe, hogy a virtuális gép számlázása továbbra is megtörténik. |
> | Műveletek | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachines/redeploy/action | Virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachines/reimage/action | A különbséglemezek lemezt használó virtuális gép rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Műveletek | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/vmSizes/read | A virtuális gép által frissíthető elérhető méretek listája |
> | Műveletek | Microsoft.Compute/virtualMachines/write | Új virtuális gép létrehozása vagy meglévő virtuális gép frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Kikapcsolja és felszabadítja a virtuálisgép-méretezési csoport példányainak számítási erőforrásait  |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete | A virtuálisgép-méretezési csoport törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete/action | A virtuálisgép-méretezési csoport példányainak törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Egy virtuálisgép-méretezési csoport bővítményének tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Létrehoz egy új virtuálisgép-méretezési csoport bővítményt, vagy frissít egy meglévőt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | A Service Fabric virtuálisgép-méretezési csoport platform-frissítési tartományának manuális bejárásával fejezze be a függőben lévő frissítést, amely beragadt |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | A virtuálisgép-méretezési csoport példány nézetének beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Virtuálisgép-méretezési csoport összes hálózati adapterének tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Elindítja a működés közbeni frissítést az összes virtuálisgép-méretezési csoport példányainak a legújabb elérhető platform-lemezkép operációsrendszer-verzióra való áthelyezéséhez. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Lekéri a virtuálisgép-méretezési csoport operációsrendszer-frissítéseinek előzményeit |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | A virtuálisgép-méretezési csoport példányain tervezett karbantartást végez |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Kikapcsolja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Virtuálisgép-méretezési csoport összes nyilvános IP-címéhez tartozó tulajdonságok beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/read | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | A virtuálisgép-méretezési csoport példányainak újbóli üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/reimage/action | A virtuálisgép-méretezési csoport példányainak rendszerképbe állítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | A virtuálisgép-méretezési csoport példányaihoz tartozó összes lemez (operációsrendszer-lemez és adatlemez) rendszerképének alaphelyzetbe állítása  |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/restart/action | Újraindítja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Egy virtuálisgép-méretezési csoport működés közbeni frissítésének megszakítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/scale/action | Annak ellenőrzése, hogy egy meglévő virtuálisgép-méretezési csoport méretezhető-e/méretezhető-e a megadott példányok száma szerint |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/skus/read | Egy meglévő virtuálisgép-méretezési csoport érvényes SKU-készletét listázza |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/start/action | Elindítja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Kikapcsolja a virtuálisgép-méretezési csoportokban lévő virtuális gépek számítási erőforrásait, és kibocsátja azokat. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép törlése. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép példány nézetét kérdezi le. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | A virtuálisgép-méretezési csoport használatával létrehozott nyilvános IP-cím tulajdonságainak beolvasása. A virtuálisgép-méretezési csoport legfeljebb egy nyilvános IP-címet képes létrehozni ipconfiguration (magánhálózati IP) |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | A virtuálisgép-méretezési csoport használatával létrehozott hálózati adapter egy vagy több IP-konfigurációjának tulajdonságainak beolvasása. Az IP-konfigurációk privát IP-címeket jelentenek |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Virtuálisgép-méretezési csoport használatával létrehozott virtuális gép egy vagy több hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok kikapcsolását. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép tulajdonságainak lekérése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Virtuálisgép-méretezési csoportokban lévő virtuálisgép-példány újratelepítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok összes lemezének (operációsrendszer-lemezének és adatlemezének) rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok újraindítása. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása virtuálisgép-méretezési csoport virtuálisgép-példányain. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | A virtuálisgép-méretezési csoportokban elindítja a virtuálisgép-példányt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép tulajdonságainak frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | A virtuálisgép-méretezési csoportba tartozó virtuális gépek létrehozásához vagy frissítéséhez elérhető méretek listázása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/write | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Consumption/balances/read | Egy felügyeleti csoport számlázási időszakának kihasználtsági összegzésének listázása. |
> | Műveletek | Microsoft.Consumption/budgets/delete | A költségvetést egy előfizetéssel vagy egy felügyeleti csoporttal törölheti. |
> | Műveletek | Microsoft.Consumption/budgets/read | Egy előfizetés vagy egy felügyeleti csoport költségvetésének listázása. |
> | Műveletek | Microsoft.Consumption/budgets/write | Előfizetéssel vagy egy felügyeleti csoporttal hozza létre és frissíti a költségvetést. |
> | Műveletek | Microsoft. fogyasztás/díjak/olvasás | Díjak listázása |
> | Műveletek | Microsoft.Consumption/credits/read | Kreditek listázása |
> | Műveletek | Microsoft. felhasználás/események/olvasás | Események listázása |
> | Műveletek | Microsoft. fogyasztás/előrejelzés/olvasás | Előrejelzések listázása |
> | Műveletek | Microsoft.Consumption/lots/read | Tételek listázása |
> | Műveletek | Microsoft. fogyasztás/piactér/olvasás | A piactér erőforrás-használati adatainak listázása az EA és a webdirect-előfizetések hatóköréhez. |
> | Műveletek | Microsoft.Consumption/operationresults/read | Operationresults listázása |
> | Műveletek | Microsoft.Consumption/operations/read | A Microsoft. reakciós erőforrás-szolgáltató által támogatott összes művelet listázása. |
> | Műveletek | Microsoft.Consumption/operationstatus/read | Operationstatus listázása |
> | Műveletek | Microsoft.Consumption/pricesheets/read | Egy előfizetés vagy egy felügyeleti csoport Pricesheets-adatbázisának listázása. |
> | Műveletek | Microsoft.Consumption/register/action | Regisztrálás a használati RP-be |
> | Műveletek | Microsoft.Consumption/reservationDetails/read | A fenntartott példányok kihasználtsági adatait foglalási sorrend vagy felügyeleti csoportok szerint sorolja fel. A részletek száma napi szintenként történik. |
> | Műveletek | Microsoft.Consumption/reservationRecommendations/read | A fenntartott példányokra vonatkozó egyszeri vagy megosztott javaslatok listázása egy előfizetéshez. |
> | Műveletek | Microsoft.Consumption/reservationSummaries/read | A fenntartott példányok kihasználtsági összegzésének listázása foglalási sorrend vagy felügyeleti csoportok szerint. Az összegző adatokat havi vagy napi szinten kell megválasztani. |
> | Műveletek | Microsoft.Consumption/reservationTransactions/read | A fenntartott példányok tranzakciós előzményeinek listázása felügyeleti csoportok szerint. |
> | Műveletek | Microsoft.Consumption/tags/read | Az EA és az előfizetések címkéit listázhatja. |
> | Műveletek | Microsoft. fogyasztás/bérlők/olvasás | Bérlők listázása |
> | Műveletek | Microsoft.Consumption/tenants/register/action | Regisztrálja a Microsoft.-alkalmazás hatókörén belüli műveletet. |
> | Műveletek | Microsoft.Consumption/terms/read | Egy előfizetés vagy egy felügyeleti csoport feltételeit sorolja fel. |
> | Műveletek | Microsoft.Consumption/usageDetails/read | Az EA-és webdirect-előfizetések hatókörének használati adatainak listázása. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Egy adott tárolóban futtatható. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Egy adott tárolóhoz tartozó naplók beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/delete | Törölje az adott tároló csoportot. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/operationResults/READ | Aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A tároló csoport diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Létrehozza vagy frissíti a tároló csoport diagnosztikai beállításait. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A tároló csoport elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/read | Minden tároló csoport beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/restart/action | Egy adott tároló csoport újraindítása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/start/action | Egy adott tároló csoportot indít el. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/stop/action | Leállítja egy adott tároló csoportot. A számítási erőforrások fel lesznek foglalva, és a számlázás leáll. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/write | Hozzon létre vagy frissítsen egy adott tároló csoportot. |
> | Műveletek | Microsoft.ContainerInstance/locations/cachedImages/read | Az előfizetéshez tartozó gyorsítótárazott rendszerképek beolvasása egy régióban. |
> | Műveletek | Microsoft.ContainerInstance/locations/capabilities/read | Egy régió képességeinek beszerzése. |
> | Műveletek | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. ContainerInstance, hogy a virtuális hálózat vagy az alhálózat törölve lett. |
> | Műveletek | Microsoft. ContainerInstance/Locations/Operations/READ | Az Azure Container instance Service műveleteinek listázása. |
> | Műveletek | Microsoft.ContainerInstance/locations/usages/read | Egy adott régió használatának beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/Operations/READ | Az Azure Container instance Service műveleteinek listázása. |
> | Műveletek | Microsoft.ContainerInstance/register/action | Regisztrálja az előfizetést a tároló példány erőforrás-szolgáltatója számára, és lehetővé teszi a tároló csoportok létrehozását. |
> | Műveletek | Microsoft. ContainerInstance/serviceassociationlinks/delete | Törölje az Azure Container instance erőforrás-szolgáltató által létrehozott Service társítási hivatkozást egy alhálózaton. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerRegistry/checkNameAvailability/read | Ellenőrzi, hogy a tároló beállításjegyzékének neve használható-e. |
> | Műveletek | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. ContainerRegistry, hogy a virtuális hálózat vagy az alhálózat törölve lett |
> | Műveletek | Microsoft.ContainerRegistry/locations/operationResults/read | Aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/operations/read | Az összes rendelkezésre álló Azure Container Registry REST API művelet listája |
> | Műveletek | Microsoft.ContainerRegistry/register/action | Regisztrálja az előfizetést a tároló-beállításjegyzék erőforrás-szolgáltatója számára, és lehetővé teszi a tároló-nyilvántartók létrehozását. |
> | Műveletek | Microsoft.ContainerRegistry/registries/artifacts/delete | Összetevő törlése egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft.ContainerRegistry/registries/builds/cancel/action | Egy meglévő Build megszakítása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Lekéri a Build naplók letöltésére szolgáló hivatkozást. |
> | Műveletek | Microsoft.ContainerRegistry/registries/builds/read | Lekéri a megadott Build vagy listázza a megadott tároló beállításjegyzékének összes buildjét. |
> | Műveletek | Microsoft.ContainerRegistry/registries/builds/write | A megadott paraméterekkel frissíti egy tároló-beállításjegyzék buildjét. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/delete | Kiépítési feladatot töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | A felépítési feladat forrás vezérlőelemének tulajdonságait sorolja fel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/read | Lekéri a megadott felépítési feladat tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes felépítési feladatot. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Kiépített lépést töröl egy felépítési feladatból. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Felsorolja a fordítási argumentumokat a létrehozási lépésekhez, beleértve a titkos argumentumokat is. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Lekéri a megadott Build lépés tulajdonságait, vagy felsorolja a megadott Build feladathoz tartozó összes összeállítási lépést. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Létrehoz vagy frissít egy Build-lépést a megadott paraméterekkel rendelkező Build feladathoz. |
> | Műveletek | Microsoft.ContainerRegistry/registries/buildTasks/write | Létrehoz vagy frissít egy Build feladatot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/delete | Töröl egy tároló-beállításjegyzéket. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Egy Event Grid-szűrőt töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Lekéri a megadott Event Grid-szűrő tulajdonságait, vagy felsorolja az összes Event Grid-szűrőt a megadott tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Létrehoz vagy frissít egy Event Grid-szűrőt egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Lekéri a felhasználó feltöltési helyét, hogy fel tudja tölteni a forrást. |
> | Műveletek | Microsoft.ContainerRegistry/registries/importImage/action | Rendszerkép importálása a tároló-beállításjegyzékbe a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Forrás feltöltési URL-címének beolvasása egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listCredentials/action | Felsorolja a megadott tároló-beállításjegyzék bejelentkezési hitelesítő adatait. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listPolicies/read | Felsorolja a megadott tároló-beállításjegyzék szabályzatait |
> | Műveletek | Microsoft.ContainerRegistry/registries/listUsages/read | Felsorolja a megadott tároló-beállításjegyzék kvótájának használatát. |
> | Műveletek | Microsoft.ContainerRegistry/registries/metadata/read | Beolvassa egy tároló-beállításjegyzék egy adott tárházának metaadatait |
> | Műveletek | Microsoft.ContainerRegistry/registries/metadata/write | Frissíti egy tároló-beállításjegyzék tárházának metaadatait |
> | Műveletek | Microsoft.ContainerRegistry/registries/operationStatuses/read | Beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft.ContainerRegistry/registries/push/write | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | Műveletek | Microsoft.ContainerRegistry/registries/quarantineRead/read | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Műveletek | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | Műveletek | Microsoft.ContainerRegistry/registries/queueBuild/action | Létrehoz egy új buildet a kérés paraméterei alapján, és hozzáadja azt a Build-várólistához. |
> | Műveletek | Microsoft.ContainerRegistry/registries/read | Lekéri a megadott tároló-beállításjegyzék tulajdonságait, vagy felsorolja a megadott erőforráscsoport vagy előfizetés alatt lévő összes tároló-bejegyzést. |
> | Műveletek | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Újra létrehozza a megadott tároló-beállításjegyzékhez tartozó bejelentkezési hitelesítő adatok egyikét. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/delete | Törli a replikálást egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Replikációs aszinkron műveleti állapot beolvasása |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/olvasás | Lekéri a megadott replikálás tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzék összes replikációját. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/írás | Létrehoz vagy frissít egy tároló-beállításjegyzék replikálását a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/runs/cancel/action | Meglévő Futtatás megszakítása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Lekéri a log SAS URL-címét a futtatáshoz. |
> | Műveletek | Microsoft.ContainerRegistry/registries/runs/read | Lekéri egy futtatási tulajdonságot egy tároló beállításjegyzékében vagy a lista futtatásakor. |
> | Műveletek | Microsoft.ContainerRegistry/registries/runs/write | Frissíti a futtatást. |
> | Műveletek | Microsoft.ContainerRegistry/registries/scheduleRun/action | Futtasson egy futtatást egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft.ContainerRegistry/registries/sign/write | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft.ContainerRegistry/registries/tasks/delete | Töröl egy feladatot egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | A tároló-beállításjegyzékhez tartozó feladat összes adatának listázása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/tasks/read | Feladat beolvasása egy tároló-beállításjegyzékhez vagy az összes feladat listázása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/tasks/write | Létrehoz vagy frissít egy feladatot egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft.ContainerRegistry/registries/updatePolicies/write | Frissíti a megadott tároló-beállításjegyzék szabályzatait |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/delete | Egy webhookot töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/webhookok/getCallbackConfig/művelet | Beolvassa a webhookhoz tartozó szolgáltatás-URI és egyéni fejlécek konfigurációját. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Felsorolja a megadott webhook legutóbbi eseményeit. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Webhook aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Elindít egy ping eseményt a webhookba. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/webhook/olvasás | Lekéri a megadott webhook tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes webhookot. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/webhook/írás | Létrehoz vagy frissít egy webhookot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/írás | Létrehoz vagy frissít egy tároló-beállításjegyzéket a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerService/containerServices/delete | Tároló szolgáltatás törlése |
> | Műveletek | Microsoft.ContainerService/containerServices/read | Tároló szolgáltatás beszerzése |
> | Műveletek | Microsoft.ContainerService/containerServices/write | Létrehoz egy új Container Service-t, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.ContainerService/locations/operationresults/read | Aszinkron műveleti eredmény állapotának beolvasása |
> | Műveletek | Microsoft.ContainerService/locations/operations/read | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft.ContainerService/locations/orchestrators/read | A támogatott rendszerszervezők listája |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján a lista hitelesítő adataival |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/read | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján |
> | Műveletek | Microsoft.ContainerService/managedClusters/agentPools/delete | Ügynök-készlet törlése |
> | Műveletek | Microsoft.ContainerService/managedClusters/agentPools/read | Ügynök készletének beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/agentPools/upgradeProfiles/READ | Az ügynök-készlet frissítési profiljának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/agentPools/write | Új ügynök készletének létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.ContainerService/managedClusters/delete | Felügyelt fürt törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/érzékelők/olvasás | Felügyelt fürt Detektorának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/diagnosticsState/READ | A fürt diagnosztikai állapotának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | Műveletek | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Műveletek | Microsoft.ContainerService/managedClusters/read | Felügyelt fürt beszerzése |
> | Műveletek | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Felügyelt fürt HRE-profiljának alaphelyzetbe állítása |
> | Műveletek | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Felügyelt fürt egyszerű szolgáltatásnév-profiljának alaphelyzetbe állítása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/upgradeProfiles/READ | A fürt frissítési profiljának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/write | Létrehoz egy új felügyelt fürtöt, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.ContainerService/openShiftClusters/delete | Nyitott SHIFT-fürt törlése |
> | Műveletek | Microsoft.ContainerService/openShiftClusters/read | Nyitott SHIFT-fürt beszerzése |
> | Műveletek | Microsoft.ContainerService/openShiftClusters/write | Új nyitott SHIFT-fürtöt hoz létre, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.ContainerService/openShiftManagedClusters/delete | Nyitott eltolású felügyelt fürt törlése |
> | Műveletek | Microsoft.ContainerService/openShiftManagedClusters/read | Nyitott eltolású felügyelt fürt beszerzése |
> | Műveletek | Microsoft.ContainerService/openShiftManagedClusters/write | Létrehoz egy új, nyitott eltolású felügyelt fürtöt, vagy frissít egy meglévőt |
> | Műveletek | Microsoft.ContainerService/operations/read | A Microsoft. Tárolószolgáltatás erőforrás-szolgáltatón elérhető műveletek felsorolása |
> | Műveletek | Microsoft.ContainerService/register/action | Előfizetés regisztrálása a Microsoft. Tárolószolgáltatás erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.ContainerService/unregister/action | Előfizetés regisztrációjának törlése a Microsoft. Tárolószolgáltatás erőforrás-szolgáltatóval |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContentModerator/applications/delete | Művelet törlése |
> | Műveletek | Microsoft.ContentModerator/applications/listSecrets/action | Titkos kulcsok listázása |
> | Műveletek | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Egyszeri bejelentkezési tokenek olvasása |
> | Műveletek | Microsoft.ContentModerator/applications/read | Olvasási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/listCommunicationPreference/action | Kommunikációs beállítások listázása |
> | Műveletek | Microsoft.ContentModerator/operations/read | olvasási műveletek |
> | Műveletek | Microsoft.ContentModerator/updateCommunicationPreference/action | Kommunikációs beállítások frissítése |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CostManagement/cloudConnectors/delete | A megadott cloudConnector törlése. |
> | Műveletek | Microsoft.CostManagement/cloudConnectors/read | A hitelesített felhasználó cloudConnectors listázása. |
> | Műveletek | Microsoft.CostManagement/cloudConnectors/write | A megadott cloudConnector létrehozása vagy frissítése. |
> | Műveletek | Microsoft.CostManagement/dimensions/read | A hatókör összes támogatott dimenziójának listázása. |
> | Műveletek | Microsoft.CostManagement/exports/action | Futtassa a megadott exportálást. |
> | Műveletek | Microsoft.CostManagement/exports/delete | A megadott exportálás törlése. |
> | Műveletek | Microsoft.CostManagement/exports/read | Az export hatókör szerinti listázása. |
> | Műveletek | Microsoft.CostManagement/exports/run/action | Exportálás futtatása. |
> | Műveletek | Microsoft.CostManagement/exports/write | A megadott exportálás létrehozása vagy frissítése. |
> | Műveletek | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | A hitelesített felhasználó externalBillingAccount belüli externalSubscriptions listázása. |
> | Műveletek | Microsoft.CostManagement/externalBillingAccounts/read | A hitelesített felhasználó externalBillingAccounts listázása. |
> | Műveletek | Microsoft.CostManagement/externalSubscriptions/read | A hitelesített felhasználó externalSubscriptions listázása. |
> | Műveletek | Microsoft.CostManagement/externalSubscriptions/write | A externalSubscription társított felügyeleti csoportjának frissítése |
> | Műveletek | Microsoft.CostManagement/query/action | Használati adatok lekérdezése hatókör alapján. |
> | Műveletek | Microsoft.CostManagement/query/read | Használati adatok lekérdezése hatókör alapján. |
> | Műveletek | Microsoft.CostManagement/register/action | Regisztrálja a Microsoft. CostManagement hatókörére vonatkozó műveletet egy előfizetés alapján. |
> | Műveletek | Microsoft.CostManagement/reports/action | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Műveletek | Microsoft.CostManagement/reports/read | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Műveletek | Microsoft.CostManagement/tenants/register/action | A Microsoft. CostManagement hatókörére vonatkozó művelet regisztrálása bérlő által. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Lehetővé teszi a visszaszállítások felvételének lefoglalását. |
> | Műveletek | Microsoft.DataBox/jobs/cancel/action | Megszakítja a folyamatban lévő rendelést. |
> | Műveletek | Microsoft.DataBox/jobs/delete | Megrendelések törlése |
> | Műveletek | Microsoft.DataBox/jobs/listCredentials/action | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | Műveletek | Microsoft.DataBox/jobs/read | Megrendelések listázása vagy beolvasása |
> | Műveletek | Microsoft.DataBox/jobs/write | Megrendelések létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBox/locations/availableSkus/action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | Műveletek | Microsoft. DataBox/Locations/availableSkus/READ | Az elérhető SKU-ket listázhatja vagy beolvashatja |
> | Műveletek | Microsoft.DataBox/locations/operationResults/read | A művelet eredményeinek listázása vagy beolvasása |
> | Műveletek | Microsoft. DataBox/Locations/regionConfiguration/Action | Ez a metódus a régió konfigurációit adja vissza. |
> | Műveletek | Microsoft.DataBox/locations/validateAddress/action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | Műveletek | Microsoft. DataBox/Locations/validateInputs/Action | Ez a metódus az összes érvényesítési típust támogatja. |
> | Műveletek | Microsoft. DataBox/Operations/READ | A műveletek listázása vagy beolvasása |
> | Műveletek | Microsoft.DataBox/register/action | Szolgáltató regisztrálása a Microsoft. Databox |
> | Műveletek | Microsoft. DataBox/előfizetések/resourceGroups/moveResources/művelet | Ez a metódus végrehajtja az erőforrás-áthelyezést. |
> | Műveletek | Microsoft. DataBox/előfizetések/resourceGroups/validateMoveResources/művelet | Ez a metódus ellenőrzi, hogy engedélyezett-e az erőforrás áthelyezése. |
> | Műveletek | Microsoft. DataBox/regisztráció/művelet | A Microsoft. Databox szolgáltató regisztrációjának megszüntetése |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | A riasztások listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | A riasztások listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | A sávszélesség-ütemtervek törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | A sávszélesség-ütemtervek létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | A Data Box Edge-eszközök törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Frissítések letöltése az eszközön |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Erőforrás-kibővített információk beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Frissítések telepítése az eszközön |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Az eszköz hálózati beállításainak listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Nodes/READ | A csomópontok listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | A művelet állapotának listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Törli a rendeléseket |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | A megrendelések listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | A megrendelések listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Létrehozza vagy frissíti a rendeléseket |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | A szerepkörök törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | A szerepkörök listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | A szerepkörök listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Szerepkörök létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Frissítések keresése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Biztonsági beállítások frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | A megosztások törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | A megosztási metaadatok frissítése a felhőből származó adatokkal |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | A megosztások létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Az eseményindítók törlése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Az eseményindítók listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Az eseményindítók listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Az eseményindítók létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Felsorolja vagy lekéri a frissítés összegzését |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Tanúsítvány feltöltése az eszköz regisztrálásához |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Törli a felhasználók megosztása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | A megosztási felhasználók listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | A megosztási felhasználók listája vagy beolvasása |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | A felhasználók megosztásának létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | A Data Box Edge-eszközök létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | A Data Box Edge-eszközök létrehozása vagy frissítése |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Databricks/locations/getNetworkPolicies/action | Hálózati leképezési házirendek beolvasása az egyes alhálózatok számára az NRP által használt hely alapján |
> | Műveletek | Microsoft.Databricks/register/action | Regisztráljon a Databricks. |
> | Műveletek | Microsoft.Databricks/workspaces/delete | Eltávolít egy Databricks-munkaterületet. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A Databricks munkaterület elérhető diagnosztikai beállításainak megadása |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A Databricks munkaterület elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft.Databricks/workspaces/read | A Databricks-munkaterületek listájának beolvasása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/írás | Létrehoz egy Databricks-munkaterületet. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataCatalog/catalogs/delete | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának törlése. |
> | Műveletek | Microsoft.DataCatalog/catalogs/read | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának olvasása. |
> | Műveletek | Microsoft.DataCatalog/catalogs/write | Data Catalog erőforrás-szolgáltatónál írja be a katalógusok erőforrását. |
> | Műveletek | Microsoft.DataCatalog/checkNameAvailability/read | A Data Catalog erőforrás-szolgáltatóhoz tartozó katalógus nevének rendelkezésre állásának keresése. |
> | Műveletek | Microsoft.DataCatalog/datacatalogs/delete | Data Catalog erőforrás-szolgáltatóhoz tartozó DataCatalog-erőforrás törlése. |
> | Műveletek | Microsoft.DataCatalog/datacatalogs/read | Data Catalog erőforrás-szolgáltató DataCatalog-erőforrásának olvasása. |
> | Műveletek | Microsoft.DataCatalog/datacatalogs/write | DataCatalog-erőforrás írása Data Catalog erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft.DataCatalog/operations/read | A Data Catalog erőforrás-szolgáltató összes elérhető műveletének beolvasása. |
> | Műveletek | Microsoft.DataCatalog/register/action | Az előfizetés regisztrálása Data Catalog erőforrás-szolgáltatóhoz |
> | Műveletek | Microsoft.DataCatalog/unregister/action | Data Catalog erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Ellenőrzi, hogy a Data Factory neve elérhető-e a használatra. |
> | Műveletek | Microsoft.DataFactory/datafactories/activitywindows/read | A Data Factory a megadott paraméterekkel olvassa be a tevékenységek ablakait. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Beolvassa a tevékenység ablakait a folyamat tevékenységéhez a megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | A megadott paraméterekkel olvassa be a folyamathoz tartozó Windows-tevékenységeket. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/delete | Törli a folyamatokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Szünetelteti a folyamatokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/read | Bármely folyamat beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Egy folyamat folytatása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/update/action | Frissíti a folyamatokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/write | Egy folyamat létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | A megadott paraméterekkel beolvassa az adatkészlethez tartozó tevékenységek ablakait. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/delete | Töröl minden adatkészletet. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/read | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Az adott adathalmazhoz tartozó adatszelet futtatásának beolvasása a megadott kezdési időponttal. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/read | Az adatszeletek lekérése az adott időszakban. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/write | Az adatszelet állapotának frissítése |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/write | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft.DataFactory/datafactories/delete | Törli a Data Factory. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Az átjáróhoz tartozó kapcsolatok adatainak beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/delete | Bármely átjáró törlése. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Felsorolja az átjárók hitelesítési kulcsait. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/read | Bármely átjáró beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/regenerateauthkey/művelet | Újragenerálja a hitelesítési kulcsokat bármely átjáró számára. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/write | Létrehoz vagy frissít egy átjárót. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/delete | Törli a társított szolgáltatásokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/read | A társított szolgáltatás beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/write | Létrehozza vagy frissíti a társított szolgáltatásokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/read | A Data Factory beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/runs/loginfo/read | Egy SAS URI-t olvas be a naplókat tartalmazó blob-tárolóba. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/delete | Töröl minden adatkészletet. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/read | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/Tables/Write | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft.DataFactory/datafactories/write | Létrehozza vagy frissíti a Data Factory. |
> | Műveletek | Microsoft.DataFactory/factories/cancelpipelinerun/action | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/cancelSandboxPipelineRun/művelet | A folyamat hibakeresési futtatásának megszakítása. |
> | Műveletek | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Létrehoz egy adatfolyam-hibakeresési munkamenetet. |
> | Műveletek | Microsoft.DataFactory/factories/dataflows/delete | Törli az adatfolyamatot. |
> | Műveletek | Microsoft.DataFactory/factories/dataflows/read | Beolvassa az adatfolyamot. |
> | Műveletek | Microsoft.DataFactory/factories/dataflows/write | Adatfolyam létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/datasets/delete | Töröl minden adatkészletet. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/read | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/write | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft. DataFactory/gyárak/debugpipelineruns/megszakítás/művelet | A folyamat hibakeresési futtatásának megszakítása. |
> | Műveletek | Microsoft.DataFactory/factories/delete | Data Factory törlése. |
> | Műveletek | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Egy adatfolyam-hibakeresési munkamenet törlése. |
> | Műveletek | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Az ADF Adatsík szolgáltatás elérésének beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Olvasási hozzáférés az ADF Adatsík szolgáltatáshoz. |
> | Műveletek | Microsoft.DataFactory/factories/getFeatureValue/action | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Műveletek | Microsoft.DataFactory/factories/getFeatureValue/read | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Műveletek | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Lekéri a GitHub hozzáférési tokenjét. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/delete | Töröl minden Integration Runtime. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | A Integration Runtime a kapcsolatok adatainak beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | A megadott Integration Runtime SSIS Integration Runtime metaadatok beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integration Runtime állapot beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Csatolt Integration Runtime-hivatkozás létrehozása a megadott megosztott Integration Runtime. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Felsorolja az egyes Integration Runtime hitelesítő kulcsait. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Beolvassa az egyes Integration Runtime figyelési szolgáltatásait. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | A megadott Integration Runtime csomópontjának törlése. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | A Integration Runtime megadott csomópontjának IP-címét adja vissza. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | A megadott Integration Runtime csomópontjának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Frissíti a saját üzemeltetésű Integration Runtime csomópontot. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/read | Bármilyen Integration Runtime beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | A megadott Integration Runtime SSIS frissítése Integration Runtime metaadatokat. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Újragenerálja a megadott Integration Runtime hitelesítési kulcsait. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Eltávolítja a megadott Integration Runtime csatolt Integration Runtime hivatkozásait. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/start/action | A Integration Runtime elindítása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Leállítja a Integration Runtime. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Szinkronizálja a megadott Integration Runtime hitelesítő adatait. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | A megadott Integration Runtime frissítése. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/write | Bármilyen Integration Runtime létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/delete | A társított szolgáltatás törlése. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/read | A társított szolgáltatás beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/write | Társított szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Beolvassa a megadott folyamat futtatási AZONOSÍTÓjának tevékenységeit. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | A megadott folyamat futtatási AZONOSÍTÓjának lekérdezése a tevékenység futásakor. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Beolvassa a lekérdezési tevékenység futásának eredményét a megadott folyamat futtatási AZONOSÍTÓJÁHOZ. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/read | A folyamat futtatásának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/createrun/action | Létrehoz egy futtatást a folyamathoz. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/delete | A folyamat törlése. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Lekéri a tevékenységek futtatásának folyamatát. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | A folyamat futtatásának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/read | Folyamatban van a folyamat beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/művelet | Hibakeresési futtatási környezetet hoz létre a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/létrehozás/művelet | Hibakeresési futtatási környezetet hoz létre a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/Futtatás/művelet | Hibakeresési futtatást hoz létre a folyamathoz. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/write | Folyamat létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Lekérdezi a hibakeresési folyamat futtatását. |
> | Műveletek | Microsoft.DataFactory/factories/querypipelineruns/action | Lekérdezi a folyamat futtatását. |
> | Műveletek | Microsoft.DataFactory/factories/querypipelineruns/read | Beolvassa a lekérdezési folyamat futtatásának eredményét. |
> | Műveletek | Microsoft.DataFactory/factories/querytriggerruns/action | Az trigger futtatását kérdezi le. |
> | Műveletek | Microsoft.DataFactory/factories/querytriggerruns/read | Beolvassa az trigger futtatásának eredményét. |
> | Műveletek | Microsoft.DataFactory/factories/read | Data Factory olvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/sandboxpipelineruns/sandboxActivityRuns/READ | A tevékenység hibakeresési futtatási adatainak beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Elindít egy adatfolyam-hibakeresési munkamenetet. |
> | Műveletek | Microsoft. DataFactory/gyárak/submitDataFlowForPreview/művelet | Adatforgalom beküldése az adatelőnézet hibakeresési munkamenet használatával történő lekéréséhez. |
> | Műveletek | Microsoft.DataFactory/factories/triggerruns/read | A trigger futtatásának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/delete | Töröl minden triggert. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/read | Bármely trigger beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/start/action | Elindítja az összes triggert. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/stop/action | Leállítja az összes triggert. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/triggerruns/read | A trigger futtatásának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/write | Minden trigger létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataFactory/gyárak/írás | Data Factory létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/locations/configureFactoryRepo/action | A gyári tárházat konfigurálja. |
> | Műveletek | Microsoft.DataFactory/locations/getFeatureValue/action | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Műveletek | Microsoft.DataFactory/locations/getFeatureValue/read | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Műveletek | Microsoft.DataFactory/operations/read | A Microsoft Data Factory-szolgáltató összes műveletének beolvasása. |
> | Műveletek | Microsoft.DataFactory/register/action | Regisztrálja az előfizetést a Data Factory erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft.DataFactory/unregister/action | A Data Factory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Számítási szabályzat törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | A számítási szabályzattal kapcsolatos információk beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Számítási szabályzat létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókjával kapcsolatos információk beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics-fiók törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Tűzfalszabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/read | Egy meglévő DataLakeAnalytics-fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Egy DataLakeAnalytics-fiók csatolt Storage-fiókjának tároló-jogkivonatait listázza. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók tárolóinak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | SystemMaxAnalyticsUnits átvitele DataLakeAnalytics-fiókok között. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/capability/read | A DataLakeAnalytics használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics-fióknév rendelkezésre állásának keresése. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/usages/read | Az előfizetéshez tartozó kvóta-használati információk beolvasása a DataLakeAnalytics használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeAnalytics/operations/read | A DataLakeAnalytics elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/register/action | Regisztrálja az előfizetést a DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeStore/accounts/delete | Data Lake Store-fiók törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/enableKeyVault/action | A kulcstartó engedélyezése egy Data Lake Store-fiókhoz. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid-szűrő törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid-szűrő beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/read | Tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/write | Tűzfalszabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/operationResults/read | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/read | Egy meglévő Data Lake Store-fiók adatainak beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/törlés | Megosztás törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/olvasás | Megosztással kapcsolatos információk lekérése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/írás | Hozzon létre vagy frissítsen egy megosztást. |
> | Műveletek | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a rendszeradminisztrátort Data Lake Store ha a Microsoft. Authorization/roleAssignments/Write. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Megbízható identitás szolgáltatójának törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Információ kérése egy megbízható identitás-szolgáltatóról. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Hozzon létre vagy frissítsen egy megbízható identitás-szolgáltatót. |
> | Műveletek | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/write | Data Lake Store-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft.DataLakeStore/locations/capability/read | A Data Lake Store használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Data Lake Store-fióknév rendelkezésre állásának keresése. |
> | Műveletek | Microsoft.DataLakeStore/locations/operationResults/read | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/locations/usages/read | Az előfizetéshez tartozó kvóta-használati információk beolvasása a Data Lake Store használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeStore/operations/read | A Data Lake Store elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/register/action | Regisztrálja az előfizetést a Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataMigration/locations/operationResults/read | Egy 202 elfogadott válaszhoz kapcsolódó hosszan futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.DataMigration/locations/operationStatuses/read | Egy 202 elfogadott válaszhoz kapcsolódó hosszan futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.DataMigration/register/action | Az előfizetés regisztrálása a Azure Database Migration Service szolgáltatóval |
> | Műveletek | Microsoft.DataMigration/services/addWorker/action | A DMS-feldolgozót hozzáadja a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/checkStatus/művelet | Annak megállapítása, hogy a szolgáltatás telepítve van-e és fut-e |
> | Műveletek | Microsoft.DataMigration/services/configureWorker/action | Egy DMS-feldolgozót konfigurál a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft.DataMigration/services/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/accessArtifacts/action | Létrehozhat egy URL-címet, amely a projekt összetevőinek beszerzéséhez vagy elvégzéséhez használható. |
> | Műveletek | Microsoft.DataMigration/services/projects/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/files/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/files/read | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft.DataMigration/services/projects/files/read/action | A fájl tartalmának olvasásához használható URL-cím beszerzése |
> | Műveletek | Microsoft.DataMigration/services/projects/files/readWrite/action | A fájl tartalmának olvasásához vagy írásához használható URL-cím beszerzése |
> | Műveletek | Microsoft.DataMigration/services/projects/files/write | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataMigration/services/projects/read | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/cancel/action | A feladat megszakítása, ha az éppen fut |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/read | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/write | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft.DataMigration/services/projects/write | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft.DataMigration/services/read | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft.DataMigration/services/removeWorker/action | DMS-feldolgozó eltávolítása a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft.DataMigration/services/serviceTasks/cancel/action | A feladat megszakítása, ha az éppen fut |
> | Műveletek | Microsoft.DataMigration/services/serviceTasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/Services/serviceTasks/READ | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/serviceTasks/írás | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft.DataMigration/services/slots/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/bővítőhely/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/bővítőhely/írás | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/indítás/művelet | A DMS szolgáltatás elindítása, amely lehetővé teszi, hogy újra feldolgozza az áttelepítést |
> | Műveletek | Microsoft.DataMigration/services/stop/action | A DMS szolgáltatás leállításával csökkentheti a költségeit |
> | Műveletek | Microsoft.DataMigration/services/updateAgentConfig/action | Frissíti a DMS-ügynök konfigurációját a megadott értékekkel. |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/írás | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataMigration/skus/read | A DMS-erőforrások által támogatott SKU-ket tartalmazó lista beolvasása. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforMariaDB/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup-alapú MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/locations/operationResults/read | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforMariaDB/operations/read | A MariaDB-műveletek listájának visszaadása. |
> | Műveletek | Microsoft.DBforMariaDB/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft.DBforMariaDB/register/action | MariaDB erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.DBforMariaDB/servers/administrators/delete | Törli a MariaDB-kiszolgáló egy meglévő rendszergazdáját. |
> | Műveletek | Microsoft.DBforMariaDB/servers/administrators/read | Lekéri a MariaDB-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MariaDB-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Új javaslat műveleti munkamenet létrehozása |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/read | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/read | Advisor visszaküldése |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Javasolt művelet visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft.DBforMariaDB/servers/databases/delete | Töröl egy meglévő MariaDB-adatbázist. |
> | Műveletek | Microsoft.DBforMariaDB/servers/databases/read | Visszaadja a MariaDB-adatbázisok listáját, vagy lekéri a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft.DBforMariaDB/servers/databases/write | Létrehoz egy MariaDB-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft.DBforMariaDB/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.DBforMariaDB/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft.DBforMariaDB/servers/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.DBforMariaDB/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft.DBforMariaDB/servers/logFiles/read | A MariaDB-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | A MariaDB-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.DBforMariaDB/servers/queryTexts/action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft.DBforMariaDB/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforMariaDB/servers/recoverableServers/read | A helyreállítható MariaDB-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/replicas/read | MariaDB-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft.DBforMariaDB/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft.DBforMariaDB/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.DBforMariaDB/servers/waitStatistics/read | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft.DBforMariaDB/servers/waitStatistics/read | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft.DBforMariaDB/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforMySQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup-alapú MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMySQL/locations/operationResults/read | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforMySQL/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforMySQL/operations/read | A MySQL-műveletek listájának visszaadása. |
> | Műveletek | Microsoft.DBforMySQL/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/register/action | MySQL erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/törlés | A MySQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a MySQL-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MySQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Új javaslat műveleti munkamenet létrehozása |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/read | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/read | Advisor visszaküldése |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Javasolt művelet visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft.DBforMySQL/servers/databases/delete | Töröl egy meglévő MySQL-adatbázist. |
> | Műveletek | Microsoft.DBforMySQL/servers/databases/read | A MySQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforMySQL/servers/databases/write | Egy MySQL-adatbázist hoz létre a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft.DBforMySQL/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforMySQL/Servers/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/naplófájlok/olvasás | A PostgreSQL-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MySQL-kiszolgálókhoz elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.DBforMySQL/servers/queryTexts/action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft.DBforMySQL/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/recoverableServers/READ | A helyreállítható MySQL-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/replicas/read | MySQL-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft.DBforMySQL/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft.DBforMySQL/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforPostgreSQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup-alapú PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/operationResults/read | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft.DBforPostgreSQL/operations/read | A PostgreSQL-műveletek listájának visszaadása. |
> | Műveletek | Microsoft.DBforPostgreSQL/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/register/action | A PostgreSQL erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/törlés | A PostgreSQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a PostgreSQL-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a PostgreSQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/olvasás | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Javaslatok készítése |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/delete | Töröl egy meglévő PostgreSQL-adatbázist. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/read | A PostgreSQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/write | Létrehoz egy PostgreSQL-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/naplófájlok/olvasás | A PostgreSQL-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/READ | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/READ | A kapcsolódó PostgreSQL-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/queryTexts/read | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/recoverableServers/READ | A helyreállítható PostgreSQL-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/replicas/read | PostgreSQL-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/READ | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/Account/logDefinitions/READ | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Devices/Account/metricDefinitions/READ | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze, hogy elérhető-e a IotHub neve |
> | Műveletek | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze, hogy elérhető-e a IotHub neve |
> | Műveletek | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy elérhető-e a kiépítési szolgáltatás neve |
> | Műveletek | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy elérhető-e a kiépítési szolgáltatás neve |
> | Műveletek | Microsoft. Devices/digitalTwins/delete | Meglévő digitális Twins-fiók és annak összes gyermekének törlése |
> | Műveletek | Microsoft. Devices/digitalTwins/operationresults/READ | Egy művelet állapotának lekérése egy digitális ikrek-fiókkal |
> | Műveletek | Microsoft. Devices/digitalTwins/READ | Az előfizetéshez társított digitális Twins-fiókok listájának beolvasása |
> | Műveletek | Microsoft. Devices/digitalTwins/SKU/READ | A digitális Twins-fiókok érvényes SKU-fiókjainak listájának beolvasása |
> | Műveletek | Microsoft. Devices/digitalTwins/Write | Új, többjegyű ikrek-fiók létrehozása |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Az IotHub-bérlő erőforrásának törlése |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub-fogyasztói csoport törlése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub-fogyasztói csoport (ok) beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | A IotHub bérlői statisztika erőforrásának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | A IotHub-bérlő kulcsának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Lekéri a IotHub bérlői kulcsait |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Kvóta-metrikák beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Read | Az IotHub-bérlő erőforrásának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Write | Az IotHub-bérlői erőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/ElasticPools/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/Delete | IotHub-erőforrás törlése |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Törli a Event Grid szűrőt |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Read | A Event Grid szűrő beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Write | Új vagy meglévő Event Grid-szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub-fogyasztói csoport törlése |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub-fogyasztói csoport (ok) beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Műveletek | Microsoft.Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | A megadott név IotHub-kulcsának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub statisztikájának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/jobs/Read | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/listkeys/Action | Az összes IotHub kulcs lekérése |
> | Műveletek | Microsoft.Devices/IotHubs/logDefinitions/read | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/operationresults/Read | Művelet eredményének beolvasása (elavult API) |
> | Műveletek | Microsoft.Devices/iotHubs/quotaMetrics/Read | Kvóta-metrikák beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Read | A IotHub-erőforrás (ok) beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Műveletek | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/skus/Read | Érvényes IotHub-SKU beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Write | IotHub-erőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/locations/operationresults/Read | Hely alapú művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/operationresults/Read | Művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/operations/Read | Az összes ResourceProvider-művelet beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/Delete | IotDps-erőforrás törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Kulcsnév IotDps kulcsainak beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/listkeys/Action | Az összes IotDps kulcs lekérése |
> | Műveletek | Microsoft.Devices/provisioningServices/logDefinitions/read | A kiépítési szolgáltatáshoz elérhető naplózási definíciók beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/metricDefinitions/read | A kiépítési szolgáltatáshoz elérhető metrikák beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/operationresults/Read | DPS-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/Read | IotDps-erőforrás beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/skus/Read | Érvényes IotDps-SKU beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/Write | IotDps-erőforrás létrehozása |
> | Műveletek | Microsoft.Devices/register/action | Regisztrálja az előfizetést a IotHub erőforrás-szolgáltatóhoz, és lehetővé teszi a IotHub-erőforrások létrehozását |
> | Műveletek | Microsoft.Devices/usages/Read | A szolgáltató előfizetés-használati adatainak beolvasása. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevSpaces/controllers/delete | Az Azure dev Spaces Controller és a adatsík Services törlése |
> | Műveletek | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Az Azure dev Spaces vezérlő infrastruktúrájának kapcsolati adatainak listázása |
> | Műveletek | Microsoft.DevSpaces/controllers/read | Az Azure dev Spaces-vezérlő tulajdonságainak olvasása |
> | Műveletek | Microsoft.DevSpaces/controllers/write | Azure dev Spaces-vezérlő tulajdonságainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Tároló-gazdagép meglévő vezérlő-leképezésének megkeresése |
> | Műveletek | Microsoft.DevSpaces/locations/operationresults/read | Aszinkron művelet állapotának olvasása |
> | Műveletek | Microsoft.DevSpaces/register/action | A Microsoft dev Spaces erőforrás-szolgáltató regisztrálása előfizetéssel |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevTestLab/labCenters/delete | Tesztkörnyezet-központok törlése. |
> | Műveletek | Microsoft.DevTestLab/labCenters/read | Laboratóriumi központok olvasása. |
> | Műveletek | Microsoft.DevTestLab/labCenters/write | Lab-központok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager-sablonok beolvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Létrehoz egy ARM-sablont az adott összetevőhöz, feltölti a szükséges fájlokat egy Storage-fiókba, és érvényesíti a generált összetevőt. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Összetevők olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/delete | Az összetevő-források törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/read | Az összetevő-források olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/write | Összetevő-források hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/read | Olvasási költségek. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/write | Költségek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/CreateEnvironment/action | Virtuális gépek létrehozása tesztkörnyezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/delete | Egyéni lemezképek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/read | Egyéni lemezképek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/write | Egyéni lemezképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/delete | Labs törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | Műveletek | Microsoft.DevTestLab/labs/ExportResourceUsage/action | A tesztkörnyezet erőforrás-használatának exportálása egy Storage-fiókba |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/read | Képletek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/write | Képletek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/galleryImages/read | Katalógus képeinek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/GenerateUploadUri/action | URI létrehozása egyéni lemezképek egy laborba való feltöltéséhez. |
> | Műveletek | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Virtuális gép importálása egy másik laborba. |
> | Műveletek | Microsoft.DevTestLab/labs/ListVhds/action | Az egyéni lemezképek létrehozásához elérhető lemezképek listázása. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/delete | Értesítési csatornák törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Értesítés küldése a megadott csatornának. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/read | Értesítési csatornák olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/write | Értesítési csatornák hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Kiértékeli a tesztkörnyezet házirendjét. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/delete | Szabályzatok törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/read | Szabályzatok beolvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/write | Házirendek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/read | Házirend-készletek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/read | A laborok beolvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Az összes vonatkozó ütemterv listája |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/read | Ütemtervek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/delete | Szolgáltatásbeli futók törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/read | Olvasási szolgáltatás futói |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/write | Szolgáltatásbeli futók hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/delete | Megosztott galériákat törölhet. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/read | Megosztott galériák olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Megosztott lemezképek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Megosztott lemezképek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Megosztott lemezképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/write | Megosztott galériákat adhat hozzá vagy módosíthat. |
> | Műveletek | Microsoft.DevTestLab/labs/users/delete | Felhasználói profilok törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Attach/action | Csatlakoztassa és hozza létre a lemez bérletét a virtuális géphez. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/delete | Lemezek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Detach/action | Válassza le és szüntesse meg a virtuális géphez csatolt lemez bérletét. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/read | Lemezek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/write | Lemezek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/read | Környezetek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/write | Környezetek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/read | Felhasználói profilok olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/delete | Törölje a titkos kulcsokat. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/read | A titkok beolvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/write | Titkos kódok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Törölje a Service Fabrics szolgáltatást. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/read | A Service Fabric beolvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ütemtervek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Indítsa el a Service fabricet. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Service Fabric leállítása |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Service fabricek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/write | Felhasználói profilok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Új vagy meglévő adatlemez csatolása a virtuális géphez. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Összetevők alkalmazása a virtuális gépre. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/delete | Virtuális gépek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Válassza le a megadott lemezt a virtuális gépről. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/read | Virtuális gépek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Virtuális gép átméretezése. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Indítsa újra a virtuális gépet. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ütemtervek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Indítsa el a virtuális gépet. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Virtuális gép leállítása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Átviszi a virtuális géphez csatolt összes adatlemezt az aktuális felhasználó tulajdonában. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Meglévő virtuális gép tulajdonjogának felszabadítása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/delete | Bastionhosts törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/READ | Bastionhosts olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/Write | Bastionhosts hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/delete | Virtuális hálózatok törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/read | Virtuális hálózatok olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/write | Virtuális hálózatok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/delete | Virtuálisgép-készletek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/read | Virtuális gépek készletének olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/write | Virtuálisgép-készletek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/write | Labs hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/locations/operations/read | Olvasási műveletek. |
> | Műveletek | Microsoft.DevTestLab/register/action | Regisztrálja az előfizetést |
> | Műveletek | Microsoft.DevTestLab/schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft.DevTestLab/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/schedules/read | Ütemtervek olvasása. |
> | Műveletek | Microsoft.DevTestLab/schedules/Retarget/action | Frissíti az ütemterv cél erőforrás-azonosítóját. |
> | Műveletek | Microsoft.DevTestLab/schedules/write | Ütemtervek hozzáadása vagy módosítása. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DocumentDB/databaseAccountNames/read | A név rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Gyűjtemény törlése. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Beolvashat egy gyűjteményt, vagy listázhatja az összes gyűjteményt. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | A gyűjtemény teljesítményének beolvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Egy gyűjtemény átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Gyűjtemény létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Tároló törlése. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Olvassa el a tárolót, vagy sorolja fel az összes tárolót. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | A tároló átviteli sebességének beolvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Tároló átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Tároló létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Adatbázis törlése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Gráf törlése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Egy gráf beolvasása vagy az összes gráf listázása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Diagram átviteli sebességének beolvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Egy gráf átviteli sebességének frissítése. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Gráf létrehozása vagy frissítése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Adatbázis beolvasása vagy az összes adatbázis listázása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Olvassa el az adatbázis átviteli sebességét. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Adatbázis átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Hozzon létre egy adatbázist. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Szóköz törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | A szóköz beolvasása vagy az összes szóköz felsorolása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Lemezterület-átviteli sebesség beolvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Lemezterület-átviteli sebesség frissítése Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Tábla törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Az összes tábla beolvasása vagy listázása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Olvassa el a tábla átviteli sebességét. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Egy tábla átviteli sebességének frissítése. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Tábla létrehozása vagy frissítése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Hozzon létre egy lemezterületet. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Tábla törlése. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Az összes tábla beolvasása vagy listázása. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Olvassa el a tábla átviteli sebességét. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Egy tábla átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Tábla létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/backup/action | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Adatbázis-fiók erőforráscsoportának módosítása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Beolvassa a gyűjtemény metrikájának definícióit. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | A gyűjtemény metrikáinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Adatbázis-fiók partíciós szintjének metrikáinak olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Adatbázis-fiók partícióinak beolvasása gyűjteményben |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Adatbázis-fiók partíciós szintjének használatának elolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Beolvassa a gyűjtemény használati adatait. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Az adatbázis metrika-definícióinak beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Az adatbázis-metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Az adatbázis-használat beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/delete | Törli az adatbázis-fiókokat. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Egy adatbázis-fiók régiói feladatátvételi prioritásainak módosítása. Ez a manuális feladatátvételi művelet végrehajtásához használatos. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/művelet | Az adatbázis-fiók biztonsági mentési szabályzatának beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Adatbázis-fiókhoz tartozó kapcsolatok karakterláncának beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Adatbázis-fiók kulcsainak listázása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Az adatbázis-fiók metrikáinak definícióinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metrics/read | Az adatbázis-fiók metrikáinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline egy adatbázis-fiók régiója.  |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Egy adatbázis-fiókhoz tartozó régió online. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Az aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Olvasási késési metrikák |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/read | Replikációs késések százalékos értékének olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Adott forrás-és célobjektum késési metrikáinak olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Adott célcsoport késési metrikáinak olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Az adatbázis-fiókhoz tartozó privát végponti kapcsolatok proxyjának törlése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Az adatbázis-fiók privát végpont-ügyfélkapcsolati proxyjának beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának ellenőrzése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának létrehozása vagy frissítése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/read | Egy adatbázis-fiók beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Adatbázis-fiók kulcsainak elforgatása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | A regionális gyűjtemény metrikáinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Regionális adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | A regionális adatbázis-fiók partíciós szintjén lévő metrikák olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | A regionális adatbázis-fiók partícióinak beolvasása egy gyűjteményben |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | A régió és az adatbázis-fiók metrikáinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/usages/read | Az adatbázis-fiók használatának beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/write | Adatbázis-fiókok frissítése. |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. DocumentDB, hogy a VirtualNetwork vagy az alhálózat törölve lett |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | DeleteVirtualNetworkOrSubnets aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft.DocumentDB/locations/operationsStatus/read | Aszinkron műveletek állapotának olvasása |
> | Műveletek | Microsoft.DocumentDB/operationResults/read | Az aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft.DocumentDB/operations/read | A Microsoft DocumentDB elérhető olvasási műveletek  |
> | Műveletek | Microsoft.DocumentDB/register/action |  Regisztrálja a Microsoft DocumentDB erőforrás-szolgáltatót az előfizetéshez |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DomainRegistration/checkDomainAvailability/Action | Ellenőrizze, hogy van-e elérhető tartomány a vásárláshoz |
> | Műveletek | Microsoft.DomainRegistration/domains/Delete | Meglévő tartomány törlése. |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Tulajdonos azonosítójának törlése |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonosi azonosítók listázása |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonos azonosítójának beolvasása |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Azonosító létrehozása vagy frissítése |
> | Műveletek | Microsoft.DomainRegistration/domains/operationresults/Read | Tartományi művelet beolvasása |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | Tartományok listájának beolvasása |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Műveletek | Microsoft.DomainRegistration/domains/renew/Action | Meglévő tartomány megújítása. |
> | Műveletek | Microsoft.DomainRegistration/domains/Write | Új tartomány hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft.DomainRegistration/generateSsoRequest/Action | Kérelem létrehozása a tartomány-ellenőrzési központba való bejelentkezéshez. |
> | Műveletek | Microsoft.DomainRegistration/listDomainRecommendations/Action | A listához tartozó javaslatok lekérdezése kulcsszavak alapján |
> | Műveletek | Microsoft.DomainRegistration/operations/Read | Az App Service-tartomány regisztrációjának összes műveletének listázása |
> | Műveletek | Microsoft.DomainRegistration/register/action | Regisztrálja a Microsoft Domains erőforrás-szolgáltatót az előfizetéshez |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Szerződés listázása művelet |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | TopLevel-tartományok beolvasása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | TopLevel-tartomány beolvasása |
> | Műveletek | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Tartományi vásárlási objektum ellenőrzése a küldés nélkül |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventGrid/domains/delete | Tartomány törlése |
> | Műveletek | Microsoft.EventGrid/domains/listKeys/action | Tartomány kulcsainak listázása |
> | Műveletek | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | A tartományok elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.EventGrid/domains/read | Tartomány beolvasása |
> | Műveletek | Microsoft.EventGrid/domains/regenerateKey/action | Tartomány kulcsának újralétrehozása |
> | Műveletek | Microsoft. EventGrid/tartományok/témakörök/törlés | Tartományi témakör törlése |
> | Műveletek | Microsoft.EventGrid/domains/topics/read | Tartományi témakör beolvasása |
> | Műveletek | Microsoft. EventGrid/tartományok/témakörök/írás | Tartományi témakör létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/domains/write | Tartomány létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/delete | EventSubscription törlése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Az esemény-előfizetés teljes URL-címének beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az esemény-előfizetések diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az esemény-előfizetések diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/metricDefinitions/READ | A eventSubscriptions elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | A témakörökhöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft.EventGrid/extensionTopics/read | ExtensionTopic beolvasása. |
> | Műveletek | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Műveletek | Microsoft.EventGrid/locations/operationResults/read | Regionális művelet eredményének beolvasása |
> | Műveletek | Microsoft.EventGrid/locations/operationsStatus/read | Regionális művelet állapotának beolvasása |
> | Műveletek | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása topictype szerint |
> | Műveletek | Microsoft.EventGrid/operationResults/read | Művelet eredményének beolvasása |
> | Műveletek | Microsoft.EventGrid/operations/read | EventGrid-műveletek listázása. |
> | Műveletek | Microsoft.EventGrid/operationsStatus/read | Művelet állapotának beolvasása |
> | Műveletek | Microsoft.EventGrid/register/action | Regisztrálja az előfizetést a EventGrid erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft.EventGrid/topics/delete | Témakör törlése |
> | Műveletek | Microsoft.EventGrid/topics/listKeys/action | Témakör kulcsainak listázása |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A témakörökhöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft.EventGrid/topics/read | Témakör beolvasása |
> | Műveletek | Microsoft.EventGrid/topics/regenerateKey/action | Témakör kulcsának újralétrehozása |
> | Műveletek | Microsoft. EventGrid/témakörök/írás | Témakör létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Műveletek | Microsoft.EventGrid/topictypes/eventtypes/read | Topictype által támogatott eventtypes olvasása |
> | Műveletek | Microsoft.EventGrid/topictypes/read | Topictype beolvasása |
> | Műveletek | Microsoft.EventGrid/unregister/action | A EventGrid erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventHub/availableClusterRegions/read | Olvasási művelet az elérhető előre kiépített fürtök Azure-régió általi listázásához. |
> | Műveletek | Microsoft.EventHub/checkNameAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft.EventHub/checkNamespaceAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft.EventHub/clusters/delete | Töröl egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft.EventHub/clusters/namespaces/read | A fürtben található névterekhez tartozó névtér-azonosítók listázása. |
> | Műveletek | Microsoft.EventHub/clusters/operationresults/read | Aszinkron fürt művelet állapotának beolvasása. |
> | Műveletek | Microsoft. EventHub/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A fürt metrikáinak erőforrásaihoz tartozó leírások listájának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/read | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/write | Létrehoz vagy módosít egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | A megadott VNet tartozó EventHub erőforrás-szolgáltató VNet-szabályainak törlése |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/action | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/read | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/write | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft.EventHub/namespaces/Delete | Névtér erőforrásának törlése |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/művelet | A EventHub frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A EventHub tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/READ |  A EventHub-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/Write | Hozzon létre EventHub engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/READ | ConsumerGroup-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/read | EventHub-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/write | EventHub tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP-szűrő erőforrásának törlése |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/read | IP-szűrő erőforrásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/write | IP-szűrő erőforrásának létrehozása |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft. EventHub/névterek/üzenetek/küldés/művelet | Üzenetek küldése |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/read | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/write | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/olvasás | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/write | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft.EventHub/namespaces/operationresults/read | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/read | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/read | VNET-szabály erőforrásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/write | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. EventHub/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft.EventHub/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.EventHub/register/action | Regisztrálja az előfizetést a EventHub erőforrás-szolgáltatóhoz, és lehetővé teszi a EventHub-erőforrások létrehozását |
> | Műveletek | Microsoft.EventHub/sku/read | SKU-erőforrások leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/sku/regions/read | SkuRegions-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/unregister/action | A EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Features/features/read | Egy előfizetés funkcióinak beolvasása. |
> | Műveletek | Microsoft. features/Operations/READ | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft. features/Providers/features/READ | Egy adott erőforrás-szolgáltatóhoz tartozó előfizetés funkciójának beolvasása. |
> | Műveletek | Microsoft. features/Providers/funkciók/regisztráció/művelet | Regisztrálja az előfizetéshez tartozó szolgáltatást egy adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/providers/features/unregister/action | Egy adott erőforrás-szolgáltató előfizetéséhez tartozó szolgáltatás regisztrációjának törlése. |
> | Műveletek | Microsoft.Features/register/action | Egy előfizetés funkciójának regisztrálása. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Vendég-konfiguráció hozzárendelésének törlése. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Vendég-konfiguráció hozzárendelésének beolvasása. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | A vendég-konfiguráció hozzárendelési jelentésének beolvasása. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Új vendég-konfiguráció hozzárendelésének létrehozása. |
> | Műveletek | Microsoft. GuestConfiguration/Operations/READ | A Microsoft. GuestConfiguration erőforrás-szolgáltató műveleteinek beolvasása |
> | Műveletek | Microsoft.GuestConfiguration/register/action | Regisztrálja az előfizetést a Microsoft. GuestConfiguration erőforrás-szolgáltatóhoz. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.HDInsight/clusters/applications/delete | HDInsight-fürt alkalmazásának törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/alkalmazások/olvasás | HDInsight-fürt alkalmazásának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/alkalmazások/írás | Alkalmazás létrehozása vagy frissítése a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/changerdpsetting/művelet | HDInsight-fürt RDP-beállításainak módosítása |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/olvasás | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/delete | HDInsight-fürt törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/törlés | HDInsight-fürthöz tartozó fürt-bővítmény törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/olvasás | Fürt kiterjesztésének beolvasása a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/írás | HDInsight-fürthöz tartozó fürt-bővítmény létrehozása |
> | Műveletek | Microsoft.HDInsight/clusters/getGatewaySettings/action | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás-HDInsight fürt diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás-HDInsight fürt diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A HDInsight-fürthöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/olvasás | A HDInsight-fürt részleteinek beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/szerepkörök/átméretezés/művelet | HDInsight-fürt átméretezése |
> | Műveletek | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/írás | HDInsight-fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft. HDInsight/Locations/képességek/olvasás | Előfizetési lehetőségek beszerzése |
> | Műveletek | Microsoft. HDInsight/Locations/checkNameAvailability/READ | Név rendelkezésre állásának keresése |
> | Műveletek | Microsoft. HDInsight/regisztráció/művelet | Az előfizetéshez tartozó HDInsight erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. HDInsight/regisztráció/művelet | HDInsight erőforrás-szolgáltató regisztrációjának törlése az előfizetéshez |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ImportExport/feladatok/törlés | Töröl egy meglévő feladatot. |
> | Műveletek | Microsoft. ImportExport/Jobs/listBitLockerKeys/művelet | A megadott feladatokhoz tartozó BitLocker-kulcsok beolvasása. |
> | Műveletek | Microsoft. ImportExport/feladatok/olvasás | Lekéri a megadott feladat tulajdonságait, vagy visszaadja a feladatok listáját. |
> | Műveletek | Microsoft. ImportExport/feladatok/írás | Létrehoz egy feladatot a megadott paraméterekkel, vagy frissíti a megadott feladatokhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. ImportExport/Locations/READ | Lekéri a megadott hely tulajdonságait, vagy visszaadja a helyek listáját. |
> | Műveletek | Microsoft. ImportExport/Operations/READ | Lekéri az erőforrás-szolgáltató által támogatott műveleteket. |
> | Műveletek | Microsoft. ImportExport/regisztráció/művelet | Regisztrálja az előfizetést az importálási/exportálási erőforrás-szolgáltató számára, és lehetővé teszi az importálási/exportálási feladatok létrehozását. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Insights/ActionGroups/Delete | Műveleti csoport törlése |
> | Műveletek | Microsoft. bepillantások/ActionGroups/olvasás | Műveleti csoport beolvasása |
> | Műveletek | Microsoft.Insights/ActionGroups/Write | Műveleti csoport létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Tevékenység naplójának riasztása aktiválva |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Delete | Műveletnapló riasztásának törlése |
> | Műveletek | Microsoft. bepillantások/ActivityLogAlerts/olvasás | Műveletnapló riasztásának beolvasása |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Write | Műveletnapló riasztásának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/AlertRules/Activated/Action | Klasszikus metrikus riasztás aktiválva |
> | Műveletek | Microsoft.Insights/AlertRules/Delete | Klasszikus metrikai riasztás törlése |
> | Műveletek | Microsoft.Insights/AlertRules/Incidents/Read | Klasszikus metrikai riasztási incidens beolvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Read | Klasszikus metrikai riasztás beolvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Resolved/Action | Klasszikus metrikai riasztás megoldva |
> | Műveletek | Microsoft.Insights/AlertRules/Throttled/Action | A klasszikus metrika riasztási szabálya szabályozva |
> | Műveletek | Microsoft.Insights/AlertRules/Write | Klasszikus metrikai riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Delete | Egy autoskálázási beállítás törlése |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. bepillantást/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/logDefinitions/READ | Napló-definíciók olvasása |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/MetricDefinitions/READ | Metrikus definíciók olvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Read | Egy autoskálázási beállítás beolvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Az autoscale leskálázás kezdeményezve |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Az autoskálázás leskálázása befejeződött |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Vertikális felskálázás kezdeményezve |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Az autoskálázás felskálázása befejeződött |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Write | Egy autoskálázási beállítás létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/alapkonfiguráció/olvasás | Metrika alaptervének beolvasása (előzetes verzió) |
> | Műveletek | Microsoft.Insights/CalculateBaseline/Read | Metrikai értékek alaptervének kiszámítása (előzetes verzió) |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Delete | Application Insights Analytics-elemek törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Read | Application Insights Analytics-elemek olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights Analytics-elemek írása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights Analytics-tábla művelet |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Delete | Application Insights Analytics-tábla sémájának törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Read | Application Insights Analytics-táblázat sémájának olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Write | Application Insights Analytics-táblázat sémájának írása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Delete | Application Insights jegyzet törlése |
> | Műveletek | Microsoft.Insights/Components/Annotations/Read | Application Insights jegyzet beolvasása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Write | Application Insights Jegyzet írása |
> | Műveletek | Microsoft.Insights/Components/Api/Read | Az Application Insights Component adatapi olvasása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Action | Application Insights API-kulcs létrehozása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Delete | Application Insights API-kulcs törlése |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Read | Application Insights API-kulcs olvasása |
> | Műveletek | Microsoft.Insights/Components/BillingPlanForComponent/Read | Application Insights-összetevő számlázási tervének beolvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights összetevő aktuális számlázási funkcióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights összetevő aktuális számlázási funkcióinak írása |
> | Műveletek | Microsoft. bepillantások/összetevők/DailyCapReached/művelet | Elérte a Application Insights-összetevő napi korlátját |
> | Műveletek | Microsoft. bepillantások/összetevők/DailyCapWarningThresholdReached/művelet | Elérte a Application Insights összetevő napi korlátjának figyelmeztetési küszöbértékét |
> | Műveletek | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Application Insights alapértelmezett ALM-integráció konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/Delete | Application ininsight-összetevő konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/Events/Read | Naplók beolvasása Application Insights OData-lekérdezési formátum használatával |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights exportálási beállítások művelet |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights exportálási beállítások törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/ExportConfiguration/olvasás | Application Insights exportálási beállítások olvasása |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Write | Application Insights exportálási beállítások írása |
> | Műveletek | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights összetevő bővített lekérdezési eredményeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Delete | Application Insights kedvenc törlése |
> | Műveletek | Microsoft.Insights/Components/Favorites/Read | Application Insights kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Write | Application Insights kedvenc írása |
> | Műveletek | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights összetevő-szolgáltatás képességeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights összetevő elérhető számlázási funkcióinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/GetToken/olvasás | Application Insights összetevő-jogkivonat olvasása |
> | Műveletek | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights összetevő metrikájának definícióinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/mérőszámok/olvasás | Application Insights-összetevő metrikáinak olvasása |
> | Műveletek | Microsoft.Insights/Components/Move/Action | Application Insights-összetevő áthelyezése másik erőforráscsoporthoz vagy előfizetésbe |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Application Insights személyes elemzési elemek törlése |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Read | Application Insights személyes elemzési elemek olvasása |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Write | Application Insights személyes elemzési elemek írása |
> | Műveletek | Microsoft. bepillantások/összetevők/MyFavorites/olvasás | Application Insights személyes kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/Operations/Read | A hosszan futó műveletek állapotának lekérése Application Insights |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Read | Application Insights összetevő árképzési tervének olvasása |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Write | Application Insights összetevő árképzési tervének írása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights proaktív észlelési konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights proaktív észlelési konfiguráció írása |
> | Műveletek | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Metrikus definíciók olvasása |
> | Műveletek | Microsoft.Insights/Components/Purge/Action | Adatok törlése a Application Insightsból |
> | Műveletek | Microsoft.Insights/Components/Query/Read | Lekérdezések futtatása Application Insights naplókon |
> | Műveletek | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights összetevő-kvóta állapotának olvasása |
> | Műveletek | Microsoft.Insights/Components/Read | Application betekintő összetevő konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights webteszt helyeinek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Delete | Application Insights ALM-integráció konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Read | Application Insights ALM-integrációs konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Write | Application Insights ALM-integráció konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/Write | Alkalmazás-ellenőrzési összetevők konfigurációjának írása |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Delete | Erőforrás diagnosztikai beállításának törlése |
> | Műveletek | Microsoft. bepillantások/DiagnosticSettings/olvasás | Erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. bepillantások/DiagnosticSettings/írás | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/EventCategories/Read | Beolvasható tevékenység naplójának eseményeinek kategóriái |
> | Műveletek | Microsoft.Insights/eventtypes/digestevents/Read | Olvasási felügyeleti eseménytípus kivonata |
> | Műveletek | Microsoft.Insights/eventtypes/values/Read | Tevékenység-naplózási események olvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Hálózati adatfolyam-napló diagnosztikai beállításának törlése |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Hálózati adatfolyam-napló diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Hálózati adatfolyam-napló diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Action | Előfizetés áttelepítési dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Read | Előfizetés áttelepítési dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/LogDefinitions/Read | Napló-definíciók olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Delete | Műveletnapló-profil törlése |
> | Műveletek | Microsoft.Insights/LogProfiles/Read | Tevékenységi napló naplójának profiljának olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Write | Műveletnapló-napló létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Adatok beolvasása a ADAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/ADReplicationResult/Read | Adatok beolvasása a ADReplicationResult táblából |
> | Műveletek | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Adatok beolvasása a ADSecurityAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/Alert/Read | Adatok beolvasása a riasztási táblából |
> | Műveletek | Microsoft.Insights/Logs/AlertHistory/Read | Adatok beolvasása a AlertHistory táblából |
> | Műveletek | Microsoft.Insights/Logs/ApplicationInsights/Read | Adatok beolvasása a ApplicationInsights táblából |
> | Műveletek | Microsoft.Insights/Logs/AzureActivity/Read | Adatok beolvasása a AzureActivity táblából |
> | Műveletek | Microsoft.Insights/Logs/AzureMetrics/Read | Adatok beolvasása a AzureMetrics táblából |
> | Műveletek | Microsoft.Insights/Logs/BoundPort/Read | Adatok beolvasása a BoundPort táblából |
> | Műveletek | Microsoft.Insights/Logs/CommonSecurityLog/Read | Adatok beolvasása a CommonSecurityLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ComputerGroup/olvasás | Adatok beolvasása a ComputerGroup táblából |
> | Műveletek | Microsoft. bepillantások/naplók/konfigurációváltozás/olvasás | Adatok beolvasása a konfigurációváltozás táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ConfigurationData/olvasás | Adatok beolvasása a ConfigurationData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerImageInventory/olvasás | Adatok beolvasása a ContainerImageInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerInventory/olvasás | Adatok beolvasása a ContainerInventory táblából |
> | Műveletek | Microsoft.Insights/Logs/ContainerLog/Read | Adatok beolvasása a ContainerLog táblából |
> | Műveletek | Microsoft.Insights/Logs/ContainerServiceLog/Read | Adatok beolvasása a ContainerServiceLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceAppCrash/olvasás | Adatok beolvasása a DeviceAppCrash táblából |
> | Műveletek | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Adatok beolvasása a DeviceAppLaunch táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCalendar/olvasás | Adatok beolvasása a DeviceCalendar táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCleanup/olvasás | Adatok beolvasása a DeviceCleanup táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceConnectSession/olvasás | Adatok beolvasása a DeviceConnectSession táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceEtw/olvasás | Adatok beolvasása a DeviceEtw táblából |
> | Műveletek | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Adatok beolvasása a DeviceHardwareHealth táblából |
> | Műveletek | Microsoft.Insights/Logs/DeviceHealth/Read | Adatok beolvasása a DeviceHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHeartbeat/olvasás | Adatok beolvasása a DeviceHeartbeat táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSkypeHeartbeat/olvasás | Adatok beolvasása a DeviceSkypeHeartbeat táblából |
> | Műveletek | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Adatok beolvasása a DeviceSkypeSignIn táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSleepState/olvasás | Adatok beolvasása a DeviceSleepState táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppFailure/olvasás | Adatok beolvasása a DHAppFailure táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppReliability/olvasás | Adatok beolvasása a DHAppReliability táblából |
> | Műveletek | Microsoft.Insights/Logs/DHDriverReliability/Read | Adatok beolvasása a DHDriverReliability táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonFailures/olvasás | Adatok beolvasása a DHLogonFailures táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonMetrics/olvasás | Adatok beolvasása a DHLogonMetrics táblából |
> | Műveletek | Microsoft.Insights/Logs/DHOSCrashData/Read | Adatok beolvasása a DHOSCrashData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHOSReliability/olvasás | Adatok beolvasása a DHOSReliability táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHWipAppLearning/olvasás | Adatok beolvasása a DHWipAppLearning táblából |
> | Műveletek | Microsoft.Insights/Logs/DnsEvents/Read | Adatok beolvasása a DnsEvents táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DnsInventory/olvasás | Adatok beolvasása a DnsInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ETWEvent/olvasás | Adatok beolvasása a ETWEvent táblából |
> | Műveletek | Microsoft. elemzések/naplók/esemény/olvasás | Adatok beolvasása az Event táblából |
> | Műveletek | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Adatok beolvasása a ExchangeAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Adatok beolvasása a ExchangeOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/szívverés/olvasás | Adatok beolvasása a szívverési táblából |
> | Műveletek | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Adatok beolvasása a IISAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/InboundConnection/Read | Adatok beolvasása a InboundConnection táblából |
> | Műveletek | Microsoft.Insights/Logs/KubeNodeInventory/Read | Adatok beolvasása a KubeNodeInventory táblából |
> | Műveletek | Microsoft.Insights/Logs/KubePodInventory/Read | Adatok beolvasása a KubePodInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/LinuxAuditLog/olvasás | Adatok beolvasása a LinuxAuditLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplication/olvasás | Adatok beolvasása a MAApplication táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealth/olvasás | Adatok beolvasása a MAApplicationHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthAlternativeVersions/olvasás | Adatok beolvasása a MAApplicationHealthAlternativeVersions táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthIssues/olvasás | Adatok beolvasása a MAApplicationHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationInstance/olvasás | Adatok beolvasása a MAApplicationInstance táblából |
> | Műveletek | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Adatok beolvasása a MAApplicationInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationReadiness/olvasás | Adatok beolvasása a MAApplicationReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADeploymentPlan/olvasás | Adatok beolvasása a MADeploymentPlan táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevice/olvasás | Adatok beolvasása a MADevice táblából |
> | Műveletek | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Adatok beolvasása a MADevicePnPHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealthAlternativeVersions/olvasás | Adatok beolvasása a MADevicePnPHealthAlternativeVersions táblából |
> | Műveletek | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Adatok beolvasása a MADevicePnPHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADeviceReadiness/olvasás | Adatok beolvasása a MADeviceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverInstanceReadiness/olvasás | Adatok beolvasása a MADriverInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverReadiness/olvasás | Adatok beolvasása a MADriverReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddin/olvasás | Adatok beolvasása a MAOfficeAddin táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Adatok beolvasása a MAOfficeAddinHealth táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Adatok beolvasása a MAOfficeAddinHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinInstance/olvasás | Adatok beolvasása a MAOfficeAddinInstance táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Adatok beolvasása a MAOfficeAddinInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinReadiness/olvasás | Adatok beolvasása a MAOfficeAddinReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeApp/olvasás | Adatok beolvasása a MAOfficeApp táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Adatok beolvasása a MAOfficeAppHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppInstance/olvasás | Adatok beolvasása a MAOfficeAppInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppReadiness/olvasás | Adatok beolvasása a MAOfficeAppReadiness táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Adatok beolvasása a MAOfficeBuildInfo táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessment/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessment táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeDeploymentStatus/olvasás | Adatok beolvasása a MAOfficeDeploymentStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroHealth/olvasás | Adatok beolvasása a MAOfficeMacroHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroHealthIssues/olvasás | Adatok beolvasása a MAOfficeMacroHealthIssues táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Adatok beolvasása a MAOfficeMacroIssueInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroIssueReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroSummary/olvasás | Adatok beolvasása a MAOfficeMacroSummary táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeSuite/olvasás | Adatok beolvasása a MAOfficeSuite táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeSuiteInstance/olvasás | Adatok beolvasása a MAOfficeSuiteInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAProposedPilotDevices/olvasás | Adatok beolvasása a MAProposedPilotDevices táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Adatok beolvasása a MAWindowsBuildInfo táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessment/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessment táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsDeploymentStatus/olvasás | Adatok beolvasása a MAWindowsDeploymentStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Adatok beolvasása a MAWindowsSysReqInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/NetworkMonitoring/olvasás | Adatok beolvasása a NetworkMonitoring táblából |
> | Műveletek | Microsoft.Insights/Logs/OfficeActivity/Read | Adatok beolvasása a OfficeActivity táblából |
> | Műveletek | Microsoft. elemzések/naplók/művelet/olvasás | Adatok beolvasása a műveleti táblából |
> | Műveletek | Microsoft.Insights/Logs/OutboundConnection/Read | Adatok beolvasása a OutboundConnection táblából |
> | Műveletek | Microsoft. elemzések/naplók/Perf/olvasás | Adatok beolvasása a Perf táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ProtectionStatus/olvasás | Adatok beolvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft. elemzések/naplók/olvasás | Adatok olvasása az összes naplóból |
> | Műveletek | Microsoft. bepillantások/naplók/ReservedAzureCommonFields/olvasás | Adatok beolvasása a ReservedAzureCommonFields táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ReservedCommonFields/olvasás | Adatok beolvasása a ReservedCommonFields táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SCCMAssessmentRecommendation/olvasás | Adatok beolvasása a SCCMAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Adatok beolvasása a SCOMAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SecurityAlert/Read | Adatok beolvasása a SecurityAlert táblából |
> | Műveletek | Microsoft.Insights/Logs/SecurityBaseline/Read | Adatok beolvasása a SecurityBaseline táblából |
> | Műveletek | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Adatok beolvasása a SecurityBaselineSummary táblából |
> | Műveletek | Microsoft.Insights/Logs/SecurityDetection/Read | Adatok beolvasása a SecurityDetection táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityEvent/olvasás | Adatok beolvasása a SecurityEvent táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ServiceFabricOperationalEvent/olvasás | Adatok beolvasása a ServiceFabricOperationalEvent táblából |
> | Műveletek | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Adatok beolvasása a ServiceFabricReliableActorEvent táblából |
> | Műveletek | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Adatok beolvasása a ServiceFabricReliableServiceEvent táblából |
> | Műveletek | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Adatok beolvasása a SfBAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Adatok beolvasása a SfBOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Adatok beolvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Adatok beolvasása a SPAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Adatok beolvasása a SQLAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Adatok beolvasása a SQLQueryPerformance táblából |
> | Műveletek | Microsoft.Insights/Logs/Syslog/Read | Adatok beolvasása a syslog-táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SysmonEvent/olvasás | Adatok beolvasása a SysmonEvent táblából |
> | Műveletek | Microsoft. elemzések/naplók/táblák. egyéni/olvasási | Adatok olvasása bármely egyéni naplóból |
> | Műveletek | Microsoft.Insights/Logs/UAApp/Read | Adatok beolvasása a UAApp táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAComputer/olvasás | Adatok beolvasása a UAComputer táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAComputerRank/olvasás | Adatok beolvasása a UAComputerRank táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UADriver/olvasás | Adatok beolvasása a UADriver táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UADriverProblemCodes/olvasás | Adatok beolvasása a UADriverProblemCodes táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAFeedback/olvasás | Adatok beolvasása a UAFeedback táblából |
> | Műveletek | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Adatok beolvasása a UAHardwareSecurity táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAIESiteDiscovery/olvasás | Adatok beolvasása a UAIESiteDiscovery táblából |
> | Műveletek | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Adatok beolvasása a UAOfficeAddIn táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAProposedActionPlan/olvasás | Adatok beolvasása a UAProposedActionPlan táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UASysReqIssue/olvasás | Adatok beolvasása a UASysReqIssue táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAUpgradedComputer/olvasás | Adatok beolvasása a UAUpgradedComputer táblából |
> | Műveletek | Microsoft.Insights/Logs/Update/Read | Adatok beolvasása a frissítési táblából |
> | Műveletek | Microsoft.Insights/Logs/UpdateRunProgress/Read | Adatok beolvasása a UpdateRunProgress táblából |
> | Műveletek | Microsoft.Insights/Logs/UpdateSummary/Read | Adatok beolvasása a updateSummary típusú táblából |
> | Műveletek | Microsoft. elemzések/naplók/használat/olvasás | Adatok beolvasása a használati táblából |
> | Műveletek | Microsoft.Insights/Logs/W3CIISLog/Read | Adatok beolvasása a W3CIISLog táblából |
> | Műveletek | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Adatok beolvasása a WaaSDeploymentStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Adatok beolvasása a WaaSInsiderStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Adatok beolvasása a WaaSUpdateStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/WDAVStatus/Read | Adatok beolvasása a WDAVStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/WDAVThreat/Read | Adatok beolvasása a WDAVThreat táblából |
> | Műveletek | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Adatok beolvasása a WindowsClientAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WindowsFirewall/olvasás | Adatok beolvasása a WindowsFirewall táblából |
> | Műveletek | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Adatok beolvasása a WindowsServerAssessmentRecommendation táblából |
> | Műveletek | Microsoft.Insights/Logs/WireData/Read | Adatok beolvasása a WireData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WUDOAggregatedStatus/olvasás | Adatok beolvasása a WUDOAggregatedStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WUDOStatus/olvasás | Adatok beolvasása a WUDOStatus táblából |
> | Műveletek | Microsoft.Insights/MetricAlerts/Delete | Metrikai riasztás törlése |
> | Műveletek | Microsoft.Insights/MetricAlerts/Read | Metrika riasztásának beolvasása |
> | Műveletek | Microsoft. bepillantások/MetricAlerts/állapot/olvasás | Metrikus riasztás állapotának olvasása |
> | Műveletek | Microsoft.Insights/MetricAlerts/Write | Metrikai riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/MetricBaselines/Read | Metrika alapkonfigurációinak olvasása |
> | Műveletek | Microsoft. inviews/MetricDefinitions/Microsoft. bepillantások/olvasás | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. inviews/MetricDefinitions/Providers/Microsoft. bepillantások/olvasás | Metrikus definíciók olvasása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Read | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. bepillantások/Metricnamespaces/olvasás | Metrikus névterek olvasása |
> | Műveletek | Microsoft.Insights/Metrics/Action | Metrika művelet |
> | Műveletek | Microsoft. inviews/mérőszámok/Microsoft. bepillantások/olvasás | Metrikák olvasása |
> | Műveletek | Microsoft. inviews/metrika/szolgáltatók/mérőszámok/olvasás | Metrikák olvasása |
> | Műveletek | Microsoft.Insights/Metrics/Read | Metrikák olvasása |
> | DataAction | Microsoft.Insights/Metrics/Write | Mérőszámok írása |
> | Műveletek | Microsoft.Insights/MigrateToNewpricingModel/Action | Előfizetés áttelepíteni az új díjszabási modellbe |
> | Műveletek | Microsoft. bepillantások/műveletek/olvasás | Olvasási műveletek |
> | Műveletek | Microsoft.Insights/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Műveletek | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Előfizetés visszaállítása a régi díjszabási modellre |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Delete | Ütemezett lekérdezési szabály törlése |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Read | Ütemezett lekérdezési szabály olvasása |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Write | Ütemezett lekérdezési szabály írása |
> | Műveletek | Microsoft.Insights/Tenants/Register/Action | A Microsoft bepillantást nyújtó szolgáltató inicializálása |
> | Műveletek | Microsoft.Insights/Unregister/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Műveletek | Microsoft. bepillantások/webtesztek/törlés | Webteszt-konfiguráció törlése |
> | Műveletek | Microsoft. bepillantások/webtesztek/GetToken/olvasás | Webteszt-jogkivonat beolvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/MetricDefinitions/olvasás | Webteszt metrikájának definícióinak olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/mérőszámok/olvasás | Webteszt-metrikák olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/írás | Egy webteszt-konfigurációba való írás |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/törlés | Munkafüzet törlése |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/írás | Munkafüzet létrehozása vagy frissítése |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Intune/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | Microsoft.Intune/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft. Intune/diagnosticsettings/írás | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft. Intune/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.IoTCentral/appTemplates/action | Az Azure IoT Central összes elérhető alkalmazási sablonjának beolvasása |
> | Műveletek | Microsoft.IoTCentral/checkNameAvailability/action | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás neve |
> | Műveletek | Microsoft.IoTCentral/checkSubdomainAvailability/action | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás altartománya |
> | Műveletek | Microsoft.IoTCentral/IoTApps/delete | IoT Central-alkalmazások törlése |
> | Műveletek | Microsoft.IoTCentral/IoTApps/read | Egyetlen IoT Central alkalmazást kap |
> | Műveletek | Microsoft.IoTCentral/IoTApps/write | IoT Central-alkalmazások létrehozása vagy frissítése |
> | Műveletek | Microsoft.IoTCentral/operations/read | Az összes elérhető művelet beolvasása IoT Central alkalmazásokban |
> | Műveletek | Microsoft.IoTCentral/register/action | Az Azure IoT Central erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.IoTSpaces/Graph/delete | Törli a Microsoft. IoTSpaces Graph-erőforrást |
> | Műveletek | Microsoft.IoTSpaces/Graph/read | A Microsoft. IoTSpaces Graph-erőforrás (ok) beolvasása |
> | Műveletek | Microsoft. IoTSpaces/gráf/írás | Microsoft. IoTSpaces Graph-erőforrás létrehozása |
> | Műveletek | Microsoft. IoTSpaces/regisztráció/művelet | A Microsoft. IoTSpaces Graph erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása az erőforrások létrehozásának engedélyezéséhez |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.KeyVault/checkNameAvailability/read | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | Műveletek | Microsoft.KeyVault/deletedVaults/read | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/delete | HSM-készlet törlése |
> | Műveletek | Microsoft.KeyVault/hsmPools/joinVault/action | Kulcstartó csatlakoztatása HSM-készlethez |
> | Műveletek | Microsoft.KeyVault/hsmPools/read | HSM-készlet tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/write | Új HSM-készlet létrehozása egy meglévő HSM-készlet tulajdonságainak frissítéséhez |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállítható törölhető kulcstartó törlése |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/read | A törölt kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. kulcstartót arról, hogy egy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.KeyVault/locations/operationResults/read | Hosszú futtatású művelet eredményének ellenőrzéséhez |
> | Műveletek | Microsoft.KeyVault/operations/read | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | Műveletek | Microsoft.KeyVault/register/action | Előfizetés regisztrálása |
> | Műveletek | Microsoft.KeyVault/unregister/action | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.KeyVault/vaults/accessPolicies/write | Meglévő hozzáférési szabályzat frissítése az egyesítéssel vagy a lecseréléssel, vagy új hozzáférési szabályzat hozzáadásával egy tárba. |
> | Műveletek | Microsoft.KeyVault/vaults/delete | Kulcstároló törlése |
> | Műveletek | Microsoft. kulcstartó/tárolók/üzembe helyezés/művelet | Lehetővé teszi az Azure-erőforrások üzembe helyezése során a kulcstartó titkos kulcsaihoz való hozzáférését |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/delete | Értesíti a Microsoft. Key Vaultot arról, hogy az Key Vault EventGrid-előfizetése törölve lett |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/read | Értesíti a Microsoft. Key Vaultot arról, hogy az Key Vault EventGrid-előfizetését tekinti meg |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/write | Értesíti a Microsoft. Key Vaultot arról, hogy a Key Vault új EventGrid-előfizetést hoz létre |
> | Műveletek | Microsoft.KeyVault/vaults/read | Key Vault tulajdonságainak megtekintése |
> | Műveletek | Microsoft. kulcstartó/tárolók/titkok/olvasás | Megtekintheti egy titkos kulcs tulajdonságait, de nem az értékét. |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/write | Hozzon létre egy új titkot, vagy frissítse egy meglévő titok értékét. |
> | Műveletek | Microsoft.KeyVault/vaults/write | Új kulcstartó létrehozása vagy egy meglévő kulcstartó tulajdonságainak frissítése |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Kusto/fürtök/aktiválás/művelet | Elindítja a fürtöt. |
> | Műveletek | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Töröl egy csatolt adatbázis-konfigurációs resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Beolvas egy csatolt adatbázis-konfigurációs resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/AttachedDatabaseConfigurations/írás | Egy csatolt adatbázis-konfigurációs resourceCopy ír. |
> | Műveletek | Microsoft.Kusto/Clusters/CheckNameAvailability/action | A fürt nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adatbázis-rendszerbiztonsági tag hozzáadására szolgál. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/CheckNameAvailability/művelet | Egy adott típushoz tartozó név rendelkezésre állásának ellenőrzése. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Törli az adatkapcsolatok resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/olvasás | Adatkapcsolatok resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/írás | Adatkapcsolatok resourceCopy írása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Az adatbázis-adatkapcsolatok ellenőrzése. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/delete | Töröl egy adatbázis-resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Az Event hub-kapcsolatok resourceCopy törlése. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Az Event hub-kapcsolatok resourceCopy olvasása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Az Event hub-kapcsolatok resourceCopy írja. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Ellenőrzi az adatbázis-esemény hub-kapcsolatát. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Az adatbázis-rendszerbiztonsági tag felsorolása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/olvasás | Egy adatbázis resourceCopy beolvasása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Az adatbázis-rendszerbiztonsági tag eltávolítása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/írás | Egy adatbázis-resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/fürtök/inaktiválás/művelet | Leállítja a fürtöt. |
> | Műveletek | Microsoft.Kusto/Clusters/delete | Egy fürt resourceCopy törlése. |
> | Műveletek | Microsoft. Kusto/fürtök/olvasás | Egy fürt resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/SKU/READ | Egy fürt SKU-resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/indítás/művelet | Elindítja a fürtöt. |
> | Műveletek | Microsoft.Kusto/Clusters/Stop/action | Leállítja a fürtöt. |
> | Műveletek | Microsoft. Kusto/fürtök/írás | Egy fürt resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/DetachFollowerDatabases/művelet | Leválasztja a követők adatbázisait. |
> | Műveletek | Microsoft. Kusto/ListFollowerDatabases/művelet | A követő adatbázisait sorolja fel. |
> | Műveletek | Microsoft.Kusto/Locations/CheckNameAvailability/action | A resourceCopy nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.Kusto/locations/operationresults/read | Olvasási műveletek resourceCopys |
> | Műveletek | Microsoft. Kusto/Operations/READ | Olvasási műveletek resourceCopys |
> | Műveletek | Microsoft. Kusto/regisztráció/művelet | Előfizetés-regisztrációs művelet |
> | Műveletek | Microsoft.Kusto/Register/action | Regisztrálja az előfizetést a Kusto erőforrás-szolgáltatón. |
> | Műveletek | Microsoft. Kusto/SKU/READ | SKU-resourceCopy olvasása. |
> | Műveletek | Microsoft.Kusto/Unregister/action | Az előfizetés regisztrációjának törlése a Kusto erőforrás-szolgáltatóra. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LabServices/labAccounts/CreateLab/action | Tesztkörnyezet létrehozása labor-fiókban. |
> | Műveletek | Microsoft.LabServices/labAccounts/delete | Tesztkörnyezet-fiókok törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/delete | Katalógus lemezképének törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/galleryImages/READ | Katalógus képeinek olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/galleryImages/Write | Katalógusbeli rendszerképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/GetRegionalAvailability/művelet | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Felhasználók hozzáadása laborhoz |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/delete | Labs törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Környezeti beállítás törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Környezetek olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Környezetek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | A környezet beállításához szükséges erőforrások kiosztása/kiosztása a tesztkörnyezet/környezet beállítása aktuális állapotán alapul. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Környezeti beállítások olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Visszaállítja a jelszót a sablon virtuális gépén. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | A sablon aktuális képét menti a megosztott galériába a labor-fiókban |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Ütemtervek olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | A sablonon belüli összes erőforrás indításával elindítja a sablont. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Leállít egy sablont a sablonban lévő összes erőforrás leállításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Környezeti beállítás hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/read | A laborok beolvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/SendEmail/action | E-mail-cím küldése a laborhoz tartozó regisztrációs hivatkozással |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/delete | Felhasználók törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/read | Felhasználók beolvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/write | Felhasználók hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/írás | Labs hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/read | Laboratóriumi fiókok olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Sharedgalleries törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/read | Sharedgalleries olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/write | Sharedgalleries hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/delete | Sharedimages törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/read | Sharedimages olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/write | Sharedimages hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/labAccounts/write | Lab-fiókok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.LabServices/locations/operations/read | Olvasási műveletek. |
> | Műveletek | Microsoft. LabServices/regisztráció/művelet | Regisztrálja az előfizetést |
> | Műveletek | Microsoft.LabServices/users/GetOperationBatchStatus/action | Kötegelt művelet állapotának beolvasása |
> | Műveletek | Microsoft.LabServices/users/GetOperationStatus/action | A hosszú ideig futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.LabServices/users/GetPersonalPreferences/action | Felhasználó személyes beállításainak beolvasása |
> | Műveletek | Microsoft.LabServices/users/ListAllEnvironments/action | A felhasználó összes környezetének listázása |
> | Műveletek | Microsoft. LabServices/felhasználók/regisztráció/művelet | Felhasználó regisztrálása felügyelt laborban |
> | Műveletek | Microsoft.LabServices/users/ResetPassword/action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Műveletek | Microsoft.LabServices/users/StartEnvironment/action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Műveletek | Microsoft. LabServices/Users/StopEnvironment/Action | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/delete | A szerződés törlése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Beolvassa a szerződés tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/read | Beolvassa a szerződést az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/write | Létrehozza vagy frissíti a szerződést az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/delete | A szerelvény törlése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Beolvassa a szerelvény tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/read | Beolvassa a szerelvényt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/write | Létrehozza vagy frissíti a szerelvényt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Törli a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Beolvassa a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Létrehozza vagy frissíti a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/delete | A tanúsítvány törlése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/read | Beolvassa a tanúsítványt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/write | A tanúsítvány létrehozása vagy frissítése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/delete | Az integrációs fiók törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/join/action | Csatlakozik az integrációs fiókhoz. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Lekéri az integrációs fiók visszahívási URL-címét. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | A Key vaultban lévő kulcsok beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Naplózza az integrációs fiók követési eseményeit. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/delete | Törli a leképezést az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Lekéri a visszahívási URL-címet a leképezési tartalomhoz az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/read | Beolvassa a térképet az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/write | Létrehozza vagy frissíti a leképezést az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/delete | Törli a partnert az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Beolvassa a partneri tartalmak visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/read | Beolvassa a partnert az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/write | Létrehozza vagy frissíti a partnert az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Beolvassa az integrációs fiók naplójának definícióit. |
> | Műveletek | Microsoft.Logic/integrationAccounts/read | Beolvassa az integrációs fiókot. |
> | Műveletek | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Újragenerálta a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/delete | Törli a sémát az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Lekéri a séma tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/read | Beolvassa a sémát az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/write | Létrehozza vagy frissíti a sémát az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/delete | A munkamenet törlése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/read | Beolvassa a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/write | A munkamenet létrehozása vagy frissítése az integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/write | Létrehozza vagy frissíti az integrációs fiókot. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/delete | Törli az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/join/action | Csatlakozik a integrációs szolgáltatási környezethoz. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Az integrációs szolgáltatás környezetének felügyelt API-műveletének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Beolvassa az integrációs szolgáltatás környezetének felügyelt API-ját. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Az integrációs szolgáltatás környezeti műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa az integrációs szolgáltatás környezeti metrikájának definícióit. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/read | Beolvassa az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/write | Az integrációs szolgáltatási környezet létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Beolvassa a munkafolyamat-javaslat műveleti csoportjait. |
> | Műveletek | Microsoft. Logic/Locations/munkafolyamatok/érvényesítés/művelet | Ellenőrzi a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/operations/read | Lekéri a műveletet. |
> | Műveletek | Microsoft.Logic/register/action | Regisztrálja a Microsoft. Logic erőforrás-szolgáltatót egy adott előfizetéshez. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/delete | Törli a hozzáférési kulcsot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/lista/művelet | Felsorolja a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/olvasás | A hozzáférési kulcs beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/újragenerált/művelet | Újragenerálta a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/írás | Létrehozza vagy frissíti a hozzáférési kulcsot. |
> | Műveletek | Microsoft.Logic/workflows/delete | Törli a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/listCallbackUrl/action | A munkafolyamat visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/listSwagger/action | A munkafolyamat hencegő definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/áthelyezés/művelet | Áthelyezi a munkafolyamatot a meglévő előfizetés-azonosítóból, erőforráscsoporthoz és/vagy névre egy másik előfizetés-azonosítóra, erőforráscsoporthoz és/vagy névre. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A munkafolyamat diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A munkafolyamat-napló definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A munkafolyamat metrika-definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/olvasás | Beolvassa a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/regenerateAccessKey/action | Újragenerálta a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft.Logic/workflows/run/action | Elindítja a munkafolyamat futtatását. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Lekéri a munkafolyamat-futtatási művelet kifejezésének nyomkövetését. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/read | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Lekéri a munkafolyamat-futtatási művelet ismétlődési kifejezésének nyomkövetését. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/read | A munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Beolvassa a munkafolyamat-futtatási műveletre vonatkozó kérelmek előzményeit. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/requestHistories/read | A munkafolyamat-futtatási műveleti kérelmek előzményeinek beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Beolvassa a munkafolyamat-futtatási művelet hatókörének ismétlődését. |
> | Műveletek | Microsoft.Logic/workflows/runs/cancel/action | Egy munkafolyamat futtatásának megszakítása. |
> | Műveletek | Microsoft.Logic/workflows/runs/delete | Egy munkafolyamat futtatását törli. |
> | Műveletek | Microsoft.Logic/workflows/runs/operations/read | A munkafolyamat-futtatási művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/read | A munkafolyamat futtatásának beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/suspend/action | Felfüggeszti a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/olvasás | Az trigger előzményeinek beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/újraküldése/művelet | A munkafolyamat-trigger újraküldése. |
> | Műveletek | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Az trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/olvasás | A trigger beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/alaphelyzetbe állítás/művelet | A trigger alaphelyzetbe állítása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/Futtatás/művelet | Végrehajtja az triggert. |
> | Műveletek | Microsoft.Logic/workflows/triggers/setState/action | A trigger állapotának beállítása. |
> | Műveletek | Microsoft.Logic/workflows/validate/action | Ellenőrzi a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/verziók/olvasás | A munkafolyamat verziójának beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Az trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/írás | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Bármely Machine Learning kötelezettségvállalási terv társításának áthelyezése |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Bármely Machine Learning kötelezettségvállalási terv társításának beolvasása |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning kötelezettségvállalási terv törlése |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/JOIN/Action | Csatlakozás bármely Machine Learning kötelezettségvállalási tervhez |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning kötelezettségvállalási terv beolvasása |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/Write | Machine Learning kötelezettségvállalási terv létrehozása vagy frissítése |
> | Műveletek | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning művelet eredményének beolvasása |
> | Műveletek | Microsoft.MachineLearning/locations/operationsstatus/read | Folyamatban lévő Machine Learning művelet állapotának beolvasása |
> | Műveletek | Microsoft.MachineLearning/operations/read | Machine Learning műveletek beolvasása |
> | Műveletek | Microsoft. MachineLearning/regisztráció/művelet | Regisztrálja az előfizetést a Machine learning webszolgáltatás erőforrás-szolgáltatója számára, és lehetővé teszi a webszolgáltatások létrehozását. |
> | Műveletek | Microsoft. MachineLearning/SKU/READ | Machine Learning kötelezettségvállalási terv beszerzése |
> | Műveletek | Microsoft.MachineLearning/webServices/action | Regionális webszolgáltatás tulajdonságainak létrehozása a támogatott régiók számára |
> | Műveletek | Microsoft.MachineLearning/webServices/delete | Machine Learning webszolgáltatás törlése |
> | Műveletek | Microsoft.MachineLearning/webServices/listkeys/read | Kulcsok beolvasása egy Machine Learning webszolgáltatáshoz |
> | Műveletek | Microsoft.MachineLearning/webServices/read | Bármely Machine Learning webszolgáltatás beolvasása |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatások/írás | Machine Learning webszolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/törlés | Tetszőleges Machine Learning-munkaterület törlése |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/listworkspacekeys/művelet | Machine Learning-munkaterület kulcsainak listázása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/olvasás | Bármilyen Machine Learning-munkaterület beolvasása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/resyncstoragekeys/művelet | Machine Learning-munkaterület konfigurált Storage-fiók kulcsainak újraszinkronizálása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/írás | Tetszőleges Machine Learning-munkaterület létrehozása vagy frissítése |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Egy adott számítási művelet állapotának beolvasása |
> | Műveletek | Microsoft. MachineLearningServices/Locations/használati adatok/olvasás | Használati jelentés az előfizetéshez tartozó pénzmosás-számítási erőforrásokhoz |
> | Műveletek | Microsoft.MachineLearningServices/locations/vmsizes/read | Támogatott virtuális gépek méretének beolvasása |
> | Műveletek | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Egy adott munkaterület-művelet állapotának beolvasása |
> | Műveletek | Microsoft.MachineLearningServices/register/action | Regisztrálja az előfizetést a Machine Learning Services erőforrás-szolgáltatóhoz |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services munkaterület (ek) ben lévő számítási erőforrások törlése |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services munkaterület számítási erőforrásaihoz tartozó titkok listázása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Számítási erőforrás csomópontjainak listázása Machine Learning Services munkaterületen |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/read | Beolvassa a Machine Learning Services munkaterület (ek) számítási erőforrásait |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/write | A számítási erőforrások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/delete | Törli a Machine Learning Services munkaterületet (ka) t |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/törlés | Kísérleteket töröl Machine Learning Services munkaterületen (k) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/olvasás | Kísérletek beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Kísérletek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services munkaterület titkainak listázása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/read | A Machine Learning Services munkaterület (ok) beolvasása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services munkaterület (ok) létrehozása vagy frissítése |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ManagedIdentity/identitások/olvasás | Meglévő rendszerhez rendelt identitás beolvasása |
> | Műveletek | Microsoft.ManagedIdentity/register/action | Regisztrálja az előfizetést a felügyelt identitás erőforrás-szolgáltatója számára |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Meglévő felhasználóhoz rendelt identitás erőforráshoz való hozzárendelésének RBAC művelete |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/read | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/READ | A felügyelt szolgáltatások regisztrációs definícióinak listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/Operations/READ | A felügyelt szolgáltatások műveleteinek listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ManagedServices/regisztráció/művelet | Regisztráljon a felügyelt szolgáltatásokra. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának hozzárendelését. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/READ | A felügyelt szolgáltatások regisztrációs hozzárendeléseinek listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/Write | Felügyelt szolgáltatások regisztrációs hozzárendelésének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának definícióját. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/READ | A felügyelt szolgáltatások regisztrációs definícióinak listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/Write | Felügyelt szolgáltatások regisztrációs definíciójának hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ManagedServices/regisztráció/művelet | A felügyelt szolgáltatások regisztrációjának törlése. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Management/checkNameAvailability/action | Ellenőrzi, hogy a felügyeleti csoport megadott neve érvényes-e és egyedi-e. |
> | Műveletek | Microsoft. Management/getEntities/művelet | A hitelesített felhasználó összes entitásának (Management Groups, előfizetések stb.) listázása. |
> | Műveletek | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Műveletek | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/delete | Az előfizetés társítása a felügyeleti csoportból. |
> | Műveletek | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Műveletek | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | Műveletek | Microsoft.Management/register/action | A megadott előfizetés regisztrálása a Microsoft. Management szolgáltatásban |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | Műveletek | Microsoft. Maps/fiókok/törlés | Maps-fiók törlése. |
> | Műveletek | Microsoft.Maps/accounts/eventGridFilters/delete | Event Grid szűrő törlése |
> | Műveletek | Microsoft. Maps/accounts/eventGridFilters/READ | Event Grid szűrő beszerzése |
> | Műveletek | Microsoft. Maps/fiókok/eventGridFilters/írás | Event Grid szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Maps/accounts/Listkeys műveletének beolvasása/Action | Maps-fiók kulcsainak listázása |
> | Műveletek | Microsoft. Maps/fiókok/olvasás | Maps-fiók beszerzése. |
> | Műveletek | Microsoft. Maps/accounts/regenerateKey/Action | Új Maps-fiók elsődleges vagy másodlagos kulcsának létrehozása |
> | Műveletek | Microsoft. Maps/fiókok/írás | Maps-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Maps/Operations/READ | Szolgáltatói műveletek beolvasása |
> | Műveletek | Microsoft.Maps/register/action | A szolgáltató regisztrálása |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Egy szerződést ad vissza. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Elfogad egy aláírt szerződést. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Rendszerkép importálása a végfelhasználói ACR-be. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Egy konfigurációt ad vissza. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Egy konfiguráció mentése. |
> | Műveletek | Microsoft.Marketplace/register/action | Regisztrálja a Microsoft. Marketplace erőforrás-szolgáltatót az előfizetésben. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/delete | A TÖRLÉSi művelet egy klasszikus fejlesztői szolgáltatási erőforráson van. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Lekéri a klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Lekéri egy klasszikus fejlesztői szolgáltatás egyszeri bejelentkezési URL-címét. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/read | A GET művelet egy klasszikus fejlesztői szolgáltatáson keresztül történik. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Létrehoz egy klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Műveletek | Microsoft.MarketplaceApps/Operations/read | Olvassa el az összes erőforrástípus műveleteit. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/megszakítás/művelet | Egy szerződés megszakítása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/olvasás | Szerződés visszaadása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/aláírás/művelet | Szerződés aláírása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/olvasás | Az adott előfizetéshez tartozó összes szerződés visszaküldése |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Szerződés beszerzése egy adott Piactéri virtuálisgép-elemmel kapcsolatban |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Egy adott Piactéri virtuálisgép-elemmel kapcsolatos szerződés aláírása vagy megszakítása |
> | Műveletek | Microsoft. MarketplaceOrdering/Operations/READ | Az API összes lehetséges műveletének listázása |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Media/checknameavailability/művelet | Ellenőrzi, hogy elérhető-e Media Services fiók neve |
> | Műveletek | Microsoft. Media/Locations/checkNameAvailability/Action | Ellenőrzi, hogy elérhető-e Media Services fiók neve |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/delete | Bármely fiók szűrő törlése |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/READ | Bármely fiók szűrő beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/Write | Bármely fiók szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/delete | Bármely eszköz-szűrő törlése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/READ | Bármely eszköz szűrő beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/Write | Bármely eszköz-szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/delete | Bármely eszköz törlése |
> | Műveletek | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Eszköz titkosítási kulcsának beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/assets/listContainerSas/action | Asset Container SAS URL-címek listázása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/listStreamingLocators/Action | Eszköz streaming-lokátorok listázása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/READ | Bármilyen eszköz beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/Write | Bármely eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/delete | A tartalmi kulcsokra vonatkozó szabályzat törlése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Szabályzat tulajdonságainak beolvasása titkokkal |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/read | A tartalmi kulcsokra vonatkozó szabályzat beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/write | A tartalmi kulcsokra vonatkozó szabályzatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/delete | Bármely Media Services fiók törlése |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/delete | Bármely Event Grid szűrő törlése |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/read | Bármilyen Event Grid szűrő olvasása |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/write | Tetszőleges Event Grid szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEventOperations/READ | Bármely élő esemény műveletének beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/delete | Bármely élő esemény törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Minden élő kimenet törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Bármilyen élő kimenet olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/liveOutputs/Write | Bármilyen élő kimenet létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/READ | Bármely élő esemény beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/reset/action | Minden élő esemény műveletének alaphelyzetbe állítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/start/action | Minden élő esemény művelet elindítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/stop/action | Az élő események működésének leállítása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/Write | Élő esemény létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveOutputOperations/READ | Bármilyen élő kimeneti művelet beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/READ | Bármely Media Services fiók beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpointOperations/READ | Bármely streaming Endpoint művelet olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/delete | Bármely streaming végpont törlése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/READ | Bármely folyamatos átviteli végpont olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Scale/Action | A streaming Endpoint művelet méretezése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Start/művelet | Bármely streaming Endpoint művelet elindítása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/leállítás/művelet | A streaming Endpoint művelet leállítása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Write | Adatfolyam-végpont létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/delete | Adatfolyam-kereső törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Tartalmi kulcsok listázása |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Útvonalak listázása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/READ | Bármely adatfolyam-kereső olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/Write | Adatfolyam-kereső létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/delete | Bármilyen folyamatos átviteli szabályzat törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/read | Bármely folyamatos átviteli szabályzat beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingPolicies/Write | Adatfolyam-szabályzat létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/mediaservices/syncStorageKeys/action | Csatolt Azure Storage-fiókhoz tartozó tárolási kulcsok szinkronizálása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/delete | Bármely átalakító törlése |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/cancelJob/művelet | Feladat megszakítása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/delete | Bármilyen feladatot törölhet |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/olvasás | Bármilyen feladattípus beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/írás | Tetszőleges feladatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítás/olvasás | Bármely átalakítás olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítás/írás | Tetszőleges átalakítás létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/Write | Bármely Media Services fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Operations/READ | Elérhető műveletek beolvasása |
> | Műveletek | Microsoft. Media/regisztráció/művelet | Regisztrálja az előfizetést a Media Services erőforrás-szolgáltatóhoz, és lehetővé teszi Media Services fiókok létrehozását |
> | Műveletek | Microsoft.Media/unregister/action | A Media Services erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/assessmentOptions/READ | Az adott helyen elérhető értékelési lehetőségek beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/értékelések/olvasás | Egy projekten belüli felmérések felsorolása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/delete | Az értékelési projekt törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/assessedmachines/olvasás | Egy felmért gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/törlés | Értékelés törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/olvasás | Az értékelés tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/írás | Új Értékelés létrehozása vagy egy meglévő értékelés frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/törlés | Csoport törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Frissítési csoport gépek hozzáadásával vagy eltávolításával |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/írás | Új csoport létrehozása vagy meglévő csoport frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/delete | A HyperV-gyűjtő törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/READ | A HyperV-gyűjtő tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/írás | Új HyperV-gyűjtő létrehozása vagy egy meglévő HyperV-gyűjtő frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/olvasás | Az értékelési projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/delete | A VMware-gyűjtő törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/READ | A VMware Collector tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/írás | Létrehoz egy új VMware-gyűjtőt, vagy frissít egy meglévő VMware-gyűjtőt. |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/írás | Új értékelési projekt létrehozása vagy egy meglévő felmérési projekt frissítése |
> | Műveletek | Microsoft. Migrálás/Locations/assessmentOptions/READ | Az adott helyen elérhető értékelési lehetőségek beolvasása |
> | Műveletek | Microsoft. Migrálás/Locations/checknameavailability/Action | Ellenőrzi, hogy az adott előfizetéshez tartozó erőforrás neve elérhető-e a megadott helyen |
> | Műveletek | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Egy adatbázis-példány tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/adatbázisok/olvasás | Egy adatbázis tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/delete | Áttelepítő projekt törlése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/MigrateEvents/delete | Egy áttelepítési eseményt töröl |
> | Műveletek | Microsoft. Migrálás/migrateprojects/MigrateEvents/READ | Beolvassa az áttelepítés eseményeinek tulajdonságait. |
> | Műveletek | Microsoft. Migrálás/migrateprojects/olvasás | Az áttelepíteni kívánt projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/RefreshSummary/művelet | Frissíti az áttelepíteni kívánt projekt összegzését |
> | Műveletek | Microsoft. Migrálás/migrateprojects/registerTool/művelet | Eszköz regisztrálása Migrálás projektbe |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | A Project-megoldási adatáttelepítés törlése |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/Delete | Egy Áttelepítő projekt-megoldás törlése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/Solutions/getconfig/művelet | A projekt átmigrálása megoldás konfigurációjának beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/megoldások/olvasás | Beolvassa a projekt áttelepíthető megoldásának tulajdonságait |
> | Műveletek | Microsoft. Migrálás/migrateprojects/megoldások/írás | Új projekt-Áttelepítő megoldás létrehozása vagy egy meglévő Áttelepítő projekt frissítése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/írás | Új Migrálás projekt létrehozása vagy meglévő Migrálás projekt frissítése |
> | Műveletek | Microsoft. Migrálás/műveletek/olvasás | A Microsoft. erőforrás-szolgáltató áttelepíthető műveleteit sorolja fel. |
> | Műveletek | Microsoft. Migrálás/projektek/értékelések/olvasás | Egy projekten belüli felmérések felsorolása |
> | Műveletek | Microsoft. Migrálás/projektek/törlés | A projekt törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/assessedmachines/olvasás | Egy felmért gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/törlés | Értékelés törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/olvasás | Az értékelés tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/írás | Új Értékelés létrehozása vagy egy meglévő értékelés frissítése |
> | Műveletek | Microsoft.Migrate/projects/groups/delete | Csoport törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/írás | Új csoport létrehozása vagy meglévő csoport frissítése |
> | Műveletek | Microsoft. Migrálás/projektek/kulcsok/művelet | A projekthez tartozó megosztott kulcsok beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/olvasás | Egy projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/írás | Új projekt létrehozása vagy meglévő projekt frissítése |
> | Műveletek | Microsoft. Migrálás/regisztráció/művelet | Előfizetés regisztrálása a Microsoftnál. erőforrás-szolgáltató átmigrálása |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MixedReality/register/action | Regisztrálja az előfizetést a vegyes valóság erőforrás-szolgáltatóhoz. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A Microsoft. MixedReality/spatialAnchorsAccounts diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A Microsoft. MixedReality/spatialAnchorsAccounts diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Microsoft. MixedReality/spatialAnchorsAccounts elérhető metrikáinak beolvasása |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Ellenőrizze, hogy elérhető-e a fájl elérési útja |
> | Műveletek | Microsoft. NetApp/Locations/checknameavailability/Action | Ellenőrizze, hogy elérhető-e az erőforrás neve |
> | Műveletek | Microsoft.NetApp/locations/operationresults/read | Egy műveleti eredmény erőforrásának beolvasása. |
> | Műveletek | Microsoft.NetApp/locations/read | Egy rendelkezésre állási ellenőrzési erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Egy készlet-erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/read | Egy készlet erőforrásának beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Kötet-erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | A csatlakoztatási cél erőforrásának beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Egy mennyiségi erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Egy pillanatkép-erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Pillanatkép-erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Egy pillanatkép-erőforrást ír. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Mennyiségi erőforrást ír. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/write | Egy készlet erőforrást ír. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/delete | Egy fiók erőforrásának törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/read | Egy fiók erőforrásának beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/write | Egy fiók erőforrásának írása. |
> | Műveletek | Microsoft.NetApp/Operations/read | Művelet-erőforrások beolvasása. |
> | Műveletek | Microsoft. NetApp/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. NetApp erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. NetApp/regisztráció/művelet | Előfizetés regisztrációjának törlése a Microsoft. NetApp erőforrás-szolgáltatóval |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway elérhető kérelmek fejlécének beolvasása |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway elérhető válasz fejlécének beolvasása |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway elérhető kiszolgálói változók beolvasása |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway SSL-re előre definiált házirend |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway elérhető SSL-beállítások |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Beolvas Application Gateway elérhető WAF-szabálykészlet |
> | Műveletek | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/applicationGateways/backendhealth/action | Egy Application Gateway-háttér állapotának beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/delete | Application Gateway törlése |
> | Műveletek | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Az Application Gateway-háttér állapotának beolvasása igény szerint az adott http-beállítás és a háttérrendszer-készlet esetében |
> | Műveletek | Microsoft.Network/applicationGateways/read | Egy Application Gateway beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/start/action | Egy Application Gateway elindítása |
> | Műveletek | Microsoft.Network/applicationGateways/stop/action | Egy Application Gateway leállítása |
> | Műveletek | Microsoft.Network/applicationGateways/write | Application Gateway létrehozása vagy egy Application Gateway frissítése |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Application Gateway WAF szabályzat törlése |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Application Gateway WAF szabályzat beolvasása |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Létrehoz egy Application Gateway WAF szabályzatot, vagy frissít egy Application Gateway WAF szabályzatot |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/delete | Alkalmazás biztonsági csoportjának törlése |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Összekapcsolja az IP-konfigurációt az alkalmazás biztonsági csoportjaival. Nem riasztásos. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Egy biztonsági szabály csatlakoztatása az alkalmazás biztonsági csoportjaihoz. Nem riasztásos. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | A új választható applicationsecuritygroup lévő IP-konfigurációk felsorolása |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/read | Egy alkalmazás biztonsági csoportjának AZONOSÍTÓját kapja meg. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/write | Létrehoz egy alkalmazás biztonsági csoportot, vagy frissít egy meglévő alkalmazás biztonsági csoportot. |
> | Műveletek | Microsoft.Network/azureFirewallFqdnTags/read | Azure Firewall FQDN-címkék beolvasása |
> | Műveletek | Microsoft.Network/azurefirewalls/delete | Azure Firewall törlése |
> | Műveletek | Microsoft. Network/azurefirewalls/READ | Azure Firewall beolvasása |
> | Műveletek | Microsoft. Network/azurefirewalls/Write | Létrehoz vagy frissít egy Azure Firewall |
> | Műveletek | Microsoft.Network/bastionHosts/delete | Megerősített gazdagép törlése |
> | Műveletek | Microsoft.Network/bastionHosts/read | Megerősített gazdagép beolvasása |
> | Műveletek | Microsoft. Network/bastionHosts/Write | Megerősített gazdagép létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/bgpServiceCommunities/read | BGP szolgáltatás-Közösségek beszerzése |
> | Műveletek | Microsoft.Network/checkFrontDoorNameAvailability/action | Ellenőrzi, hogy elérhető-e a bejárati ajtó neve |
> | Műveletek | Microsoft.Network/checkTrafficManagerNameAvailability/action | Ellenőrzi egy Traffic Manager relatív DNS-név rendelkezésre állását. |
> | Műveletek | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/revoke/action | Express Route-kapcsolatok állapotának megjelölése visszavont értékként |
> | Műveletek | Microsoft. Network/Connections/sharedkey/Action | Get VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/write | Meglévő VirtualNetworkGatewayConnection-SharedKey létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Gets Vpn Device Configuration of VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft. Network/Connections/Write | Meglévő VirtualNetworkGatewayConnection létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/delete | A DDoS-re szabott szabályzat törlése |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/read | A DDoS-szabályzatok testreszabott definíciójának beolvasása |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/write | Létrehoz egy DDoS testreszabott szabályzatot, vagy frissít egy meglévő DDoS testreszabott szabályzatot |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/delete | DDoS Protection csomag törlése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/join/action | Egy DDoS Protection-csomaghoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/read | DDoS Protection csomag beolvasása |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/write | Létrehoz egy DDoS Protection csomagot vagy frissíti a DDoS Protection tervet  |
> | Műveletek | Microsoft.Network/dnsoperationresults/read | DNS-művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Network/dnsoperationstatuses/read | DNS-művelet állapotának beolvasása  |
> | Műveletek | Microsoft.Network/dnszones/A/delete | Távolítsa el egy adott név rekordját, és írja be a "A" értéket egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/A/read | Adja meg a "A" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/A/write | Egy "A" típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/read | Adja meg az "AAAA" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/write | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/all/read | DNS-rekordhalmazok beolvasása típusok között |
> | Műveletek | Microsoft.Network/dnszones/CAA/delete | Távolítsa el egy adott név rekordját, és írja be a "CAA" típust egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/CAA/read | Adja meg a "CAA" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Műveletek | Microsoft.Network/dnszones/CAA/write | Hozzon létre vagy frissítsen egy "CAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/read | Adja meg a "CNAME" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/delete | Törölje a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. |
> | Műveletek | Microsoft.Network/dnszones/MX/delete | Távolítsa el egy adott név rekordját, és írja be az "MX" típust egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/MX/read | Adja meg a "MX" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/MX/write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/NS/delete | Az NS típusú DNS-rekord törlése |
> | Műveletek | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/NS/write | NS típusú DNS-rekord létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/dnszones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/PTR/read | Adja meg a "PTR" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/PTR/write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/read | Kérje le a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. Vegye figyelembe, hogy a parancs nem kéri le a zónán belül található rekordhalmazokat. |
> | Műveletek | Microsoft.Network/dnszones/recordsets/read | DNS-rekordhalmazok beolvasása típusok között |
> | Műveletek | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Műveletek | Microsoft.Network/dnszones/SOA/write | SOA típusú DNS-rekord létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/dnszones/SRV/delete | Távolítsa el egy adott név rekordját, és írja be a "SRV" értéket egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/SRV/read | A "SRV" típusú rekordtípusok beolvasása JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/SRV/write | SRV típusú rekordtípusok létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/dnszones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/TXT/read | Hozzon létre egy "TXT" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/TXT/write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/dnszones/write | Hozzon létre vagy frissítsen egy DNS-zónát az erőforráscsoporthoz.  A DNS-zóna erőforrásaihoz tartozó címkék frissítésére szolgál. Vegye figyelembe, hogy ez a parancs nem használható rekordhalmazok létrehozásához vagy frissítéséhez a zónán belül. |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit-engedély törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit-hitelesítés beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/write | Meglévő ExpressRouteCircuit-hitelesítés létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/join/action | Egy Express Route-áramkörhöz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/arpTables/READ | Beolvas egy ExpressRouteCircuit-társat |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit-kapcsolatok törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit-kapcsolatok beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Meglévő ExpressRouteCircuit-kapcsolatok erőforrásának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit-társítás törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Peer Express Route Circuit-kapcsolat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit-társítás beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/routeTables/READ | ExpressRouteCircuit-társítási útvonaltábla beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/routeTablesSummary/READ | ExpressRouteCircuit-társítási útvonaltábla összegzésének beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit-társítási stat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/write | Meglévő ExpressRouteCircuit-társítás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit beszerzése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit-stat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/write | Meglévő ExpressRouteCircuit létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/join/action | Egy expressz útvonalon keresztüli összekötő csatlakoztatása. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/arpTables/READ | Expressz útvonal közötti kapcsolatok összekapcsolásának ARP-táblájának beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Expressz útvonalon keresztüli kapcsolatok társításának törlése |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/read | Expressz útvonal közötti kapcsolatok társításának beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/routeTables/READ | Expressz útvonal közötti kapcsolatok összekapcsolási útválasztási táblázatának beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/routeTableSummary/READ | Expressz útvonal keresztben való összekapcsolási útválasztási táblázatának összefoglalása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/write | Létrehoz egy expressz útvonalon keresztüli kapcsolatok összekapcsolását, vagy frissíti a meglévő Express Route-kapcsolatok közötti kapcsolatot |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/read | Expressz útvonalon keresztüli kapcsolatok lekérdezése |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route-kapcsolatok törlése |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route-kapcsolatok beolvasása |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route-kapcsolatok létrehozása vagy egy meglévő Express Route-kapcsolatok frissítése |
> | Műveletek | Microsoft.Network/expressRouteGateways/join/action | Egy Express Route-átjáróhoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft.Network/expressRouteGateways/read | Express Route-átjáró beolvasása |
> | Műveletek | Microsoft.Network/expressRoutePorts/delete | Deletes ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePorts/join/action | Az Express Route-portok illesztése. Nem riasztásos. |
> | Műveletek | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink beolvasása |
> | Műveletek | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/expressRoutePortsLocations/read | Express Route-portok helyeinek beolvasása |
> | Műveletek | Microsoft.Network/expressRouteServiceProviders/read | Expressz Route Service szolgáltatók beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/delete | Tűzfal-házirend törlése |
> | Műveletek | Microsoft. Network/firewallPolicies/csatlakozás/művelet | Csatlakozik egy tűzfal-házirendhez. Nem riasztásos. |
> | Műveletek | Microsoft. Network/firewallPolicies/READ | Tűzfal-házirend beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/delete | Egy tűzfalszabály-szabálytípus törlése |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/READ | Tűzfal házirend-szabály csoportjának beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/Write | Létrehoz egy tűzfalszabály-szabályt, vagy frissíti egy meglévő tűzfalszabály-szabály csoportját. |
> | Műveletek | Microsoft. Network/firewallPolicies/Write | Létrehoz egy tűzfal-házirendet, vagy frissíti a meglévő tűzfal-házirendet. |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/delete | Háttér-készlet törlése |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/read | Háttér-készlet beolvasása |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/write | Háttér-készlet létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoors/delete | Bejárati ajtó törlése |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/delete | Előtér-végpont törlése |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | A HTTPS letiltása egy előtér-végponton |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | HTTPS engedélyezése előtér-végponton |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/read | Előtér-végpont beolvasása |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/write | Előtér-végpont létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoors/healthProbeSettings/delete | Állapot-mintavételi beállítások törlése |
> | Műveletek | Microsoft.Network/frontDoors/healthProbeSettings/read | Állapot mintavételi beállításainak beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/healthProbeSettings/Write | Állapot mintavételi beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/read | Terheléselosztási beállítások beolvasása |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/write | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/Purge/művelet | Gyorsítótárazott tartalom kiürítése egy bejárati ajtóról |
> | Műveletek | Microsoft. Network/frontDoors/READ | Bejárati ajtó beolvasása |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/delete | Útválasztási szabály törlése |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/read | Útválasztási szabály beolvasása |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/write | Útválasztási szabály létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoors/validateCustomDomain/action | Előtérbeli végpont ellenőrzése egy bejárati ajtónál |
> | Műveletek | Microsoft. Network/frontDoors/Write | Bejárati ajtó létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Webalkalmazási tűzfal által felügyelt szabálykészlet beolvasása |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Egy webalkalmazási tűzfal házirend törlése |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Webalkalmazási tűzfal házirend beolvasása |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Webalkalmazási tűzfal házirendjének létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/read | Terheléselosztó háttérbeli címkészlet-definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/delete | Terheléselosztó törlése |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Egy Load Balancer előtér-IP-konfigurációhoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Beolvassa a terheléselosztó előtérbeli IP-konfigurációjának definícióját |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/join/action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/read | Beolvassa a terheléselosztó bejövő NAT-készletének definícióját |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/delete | A terheléselosztó bejövő NAT-szabályának törlése |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/join/action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/read | Beolvassa a terheléselosztó bejövő NAT-szabályának definícióját |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/write | Létrehoz egy terheléselosztó bejövő NAT-szabályát, vagy frissíti a meglévő terheléselosztó bejövő NAT-szabályát |
> | Műveletek | Microsoft.Network/loadBalancers/loadBalancingRules/read | Terheléselosztó terheléselosztási szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/networkInterfaces/read | A terheléselosztó alatt lévő összes hálózati adapterre mutató hivatkozásokat kap |
> | Műveletek | Microsoft.Network/loadBalancers/outboundRules/read | A terheléselosztó kimenő szabályának definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | Műveletek | Microsoft.Network/loadBalancers/probes/read | Terheléselosztó mintavételének beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/virtualMachines/read | A terheléselosztó alatt lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Műveletek | Microsoft.Network/loadBalancers/write | Terheléselosztó létrehozása vagy egy meglévő terheléselosztó frissítése |
> | Műveletek | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/write | Meglévő LocalNetworkGateway létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Automatikusan jóváhagyott privát kapcsolati szolgáltatások beolvasása |
> | Műveletek | Microsoft.Network/locations/availableDelegations/read | Elérhető delegálások beolvasása |
> | Műveletek | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Elérhető privát végponti erőforrások beolvasása |
> | Műveletek | Microsoft. Network/Locations/availableServiceAliases/READ | Elérhető szolgáltatás-aliasok beolvasása |
> | Műveletek | Microsoft. Network/Locations/bareMetalTenants/Action | Operációs rendszer nélküli bérlő kiosztása vagy ellenőrzése |
> | Műveletek | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | A gyorsított hálózatkezelés támogatásának ellenőrzése |
> | Műveletek | Microsoft.Network/locations/checkDnsNameAvailability/read | Ellenőrzi, hogy a DNS-címke elérhető-e a megadott helyen |
> | Műveletek | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | A privát hivatkozás szolgáltatás láthatóságának ellenőrzése |
> | Műveletek | Microsoft.Network/locations/operationResults/read | Aszinkron POST-vagy TÖRLÉSi művelet működési eredményének beolvasása |
> | Műveletek | Microsoft.Network/locations/operations/read | Egy aszinkron művelet állapotát jelképező műveleti erőforrás beolvasása |
> | Műveletek | Microsoft.Network/locations/serviceTags/read | Szolgáltatás-címkék beolvasása |
> | Műveletek | Microsoft.Network/locations/supportedVirtualMachineSizes/read | A virtuális gépek támogatott méretének beolvasása |
> | Műveletek | Microsoft. Network/Locations/használat/olvasás | Az erőforrások használati metrikáinak beolvasása |
> | Műveletek | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Lekéri az elérhető Virtual Network Endpoint Services listáját |
> | Műveletek | Microsoft.Network/networkIntentPolicies/delete | Hálózati leképezési házirend törlése |
> | Műveletek | Microsoft.Network/networkIntentPolicies/read | Hálózati leképezési szabályzat lekérése – Leírás |
> | Műveletek | Microsoft.Network/networkIntentPolicies/write | Létrehoz egy hálózati leképezési házirendet, vagy frissíti a meglévő hálózati leképezési házirendet. |
> | Műveletek | Microsoft.Network/networkInterfaces/delete | Hálózati adapter törlése |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | A virtuális gép hálózati adapterén konfigurált hálózati biztonsági csoportok beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | A virtuális gép hálózati adapterén konfigurált útválasztási táblázat beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Csatlakozik egy hálózati adapter IP-konfigurációjához. Nem riasztásos. |
> | Műveletek | Microsoft. Network/networkInterfaces/ipconfigurations/READ | Hálózati adapter IP-konfigurációjának meghatározását kéri le.  |
> | Műveletek | Microsoft.Network/networkInterfaces/join/action | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/networkInterfaces/loadBalancers/read | Az összes olyan terheléselosztó beolvasása, amelyhez a hálózati adapter tartozik |
> | Műveletek | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Műveletek | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Töröl egy hálózati adaptert, koppintson a konfiguráció elemre. |
> | Műveletek | Microsoft. Network/networkInterfaces/tapConfigurations/READ | Beolvas egy hálózati adaptert a konfiguráció elemre koppintva. |
> | Műveletek | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Hálózati adaptert hoz létre, koppintson a konfiguráció lehetőségre, vagy frissítsen egy meglévő hálózati adaptert a konfiguráció gombra. |
> | Műveletek | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Műveletek | Microsoft.Network/networkProfiles/delete | Hálózati profil törlése |
> | Műveletek | Microsoft.Network/networkProfiles/read | Hálózati profil beolvasása |
> | Műveletek | Microsoft.Network/networkProfiles/removeContainers/action | Tárolók eltávolítása |
> | Műveletek | Microsoft.Network/networkProfiles/setContainers/action | Tárolók beállítása |
> | Műveletek | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Tároló hálózati adapterek beállítása |
> | Műveletek | Microsoft. Network/networkProfiles/Write | Hálózati profil létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/delete | Hálózati biztonsági csoport törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/delete | Biztonsági szabály törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/read | Biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/write | Biztonsági szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/write | Hálózati biztonsági csoport létrehozása vagy egy meglévő hálózati biztonsági csoport frissítése |
> | Műveletek | Microsoft.Network/networkWatchers/availableProvidersList/action | Egy adott Azure-régió összes elérhető internetszolgáltatóját adja vissza. |
> | Műveletek | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Egy adott hely és az Azure-régiók közötti relatív késési pontszámot adja vissza az internetszolgáltatók számára. |
> | Műveletek | Microsoft.Network/networkWatchers/configureFlowLog/action | A folyamat naplózását konfigurálja a cél erőforráshoz. |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/delete | Egy figyelő törlése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/query/action | A megadott végpontok közötti figyelési kapcsolat lekérdezése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/read | Kapcsolati figyelő részleteinek beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/start/action | A megadott végpontok közötti kapcsolat figyelésének megkezdése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Megadott végpontok közötti figyelési kapcsolat leállítása/szüneteltetése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/write | Létrehoz egy összekötő-figyelőt |
> | Műveletek | Microsoft.Network/networkWatchers/connectivityCheck/action | Ellenőrzi, hogy lehetséges-e közvetlen TCP-kapcsolatok létesítése egy virtuális gépről egy adott végpontra, beleértve egy másik virtuális gépet vagy egy tetszőleges távoli kiszolgálót. |
> | Műveletek | Microsoft.Network/networkWatchers/delete | Hálózati figyelő törlése |
> | Műveletek | Microsoft.Network/networkWatchers/ipFlowVerify/action | Visszaadja, hogy a csomag engedélyezett-e vagy le van tiltva egy adott célhelyről. |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/delete | Egy lencsét töröl |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/query/action | Egy megadott végponton figyelt hálózati forgalom lekérdezése |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/read | A lencse részleteinek beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/start/action | A hálózati forgalom figyelésének megkezdése egy megadott végponton |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/stop/action | A hálózati forgalom figyelésének leállítása/szüneteltetése egy megadott végponton |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/write | Létrehoz egy lencsét |
> | Műveletek | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | A hálózati konfiguráció diagnosztika. |
> | Műveletek | Microsoft.Network/networkWatchers/nextHop/action | Egy adott cél és cél IP-cím esetében adja vissza a következő ugrás típusát és a következő remény IP-címét. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/delete | Csomagok rögzítésének törlése |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Információk beolvasása a csomag rögzítési erőforrásának tulajdonságairól és állapotáról. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/read | A csomag rögzítési definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Állítsa le a futó csomag rögzítési munkamenetét. |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/Write | Létrehoz egy csomagot. |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh törlése |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh részleteinek beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/start/action | PingMesh elindítása a megadott virtuális gépek között |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/stop/action | A megadott virtuális gépek közötti PingMesh leállítása |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/write | Létrehoz egy PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Kötegelt lekérdezés figyelési kapcsolata a megadott végpontok között |
> | Műveletek | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Lekérdezi a folyamat naplózási állapotát egy erőforráson. |
> | Műveletek | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Lekérdezi a korábban futtatott vagy éppen futó hibaelhárítási művelet hibaelhárítási eredményét. |
> | Műveletek | Microsoft.Network/networkWatchers/read | A Network Watcher definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/securityGroupView/action | Megtekintheti a virtuális gépen alkalmazott konfigurált és érvényes hálózati biztonsági csoportra vonatkozó szabályokat. |
> | Műveletek | Microsoft.Network/networkWatchers/topology/action | Lekérdezi az erőforrások hálózati szintű nézetét és azok kapcsolatait egy erőforráscsoporthoz. |
> | Műveletek | Microsoft.Network/networkWatchers/troubleshoot/action | Hibaelhárítást indít egy hálózati erőforráson az Azure-ban. |
> | Műveletek | Microsoft.Network/networkWatchers/write | Hálózati figyelő létrehozása vagy meglévő hálózati figyelő frissítése |
> | Műveletek | Microsoft. Network/Operations/READ | Elérhető műveletek beolvasása |
> | Műveletek | Microsoft.Network/p2sVpnGateways/delete | Töröl egy P2SVpnGateway. |
> | Műveletek | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | VPN-profil előállítása a P2SVpnGateway számára |
> | Műveletek | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | P2S VPN-kapcsolati állapotának beolvasása a P2SVpnGateway |
> | Műveletek | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | P2S VPN-kapcsolat állapotának beolvasása a P2SVpnGateway |
> | Műveletek | Microsoft.Network/p2sVpnGateways/read | P2SVpnGateway beolvasása. |
> | Műveletek | Microsoft.Network/p2sVpnGateways/write | Egy P2SVpnGateway helyez el. |
> | Műveletek | Microsoft.Network/privateDnsOperationResults/read | Egy saját DNS művelet eredményének beolvasása |
> | Műveletek | Microsoft.Network/privateDnsOperationStatuses/read | Egy saját DNS művelet állapotának beolvasása |
> | Műveletek | Microsoft.Network/privateDnsZones/A/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be az "A" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft.Network/privateDnsZones/A/read | Az "A" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/A/write | Hozzon létre vagy frissítsen egy "A" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/read | Az "AAAA" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/write | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/mind/olvasás | saját DNS-rekordhalmaz beolvasása típusok között |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy saját DNS zónából. |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/read | A "CNAME" típusú rekordhalmaz beolvasása egy saját DNS zónában, JSON formátumban megadva. |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy saját DNS zónán belül. |
> | Műveletek | Microsoft.Network/privateDnsZones/delete | Saját DNS zóna törlése. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/delete | Remove the record set of a given name and type ‘MX’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/read | Az "MX" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/privateDnsZones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/PTR/READ | A "PTR" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/PTR/write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/privateDnsZones/read | A saját DNS zóna tulajdonságainak beolvasása JSON formátumban. Vegye figyelembe, hogy a parancs nem kéri le azokat a virtuális hálózatokat, amelyekhez a saját DNS zóna csatolva van, vagy a zónán belül található rekordhalmazok. |
> | Műveletek | Microsoft.Network/privateDnsZones/recordsets/read | saját DNS-rekordhalmaz beolvasása típusok között |
> | Műveletek | Microsoft.Network/privateDnsZones/SOA/read | A "SOA" típusú rekordhalmaz beszerzése egy saját DNS zónában, JSON formátumban megadva. |
> | Műveletek | Microsoft.Network/privateDnsZones/SOA/write | Egy "SOA" típusú rekordhalmaz frissítése egy saját DNS zónán belül. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/delete | Távolítsa el az adott név és a "SRV" típusú rekordot egy saját DNS zónából. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/read | A "SRV" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/write | Hozzon létre vagy frissítsen egy "SRV" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy saját DNS zónából. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/read | A "TXT" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Saját DNS zóna virtuális hálózatra mutató hivatkozásának törlése. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | A saját DNS zóna hivatkozásának beolvasása a virtuális hálózat tulajdonságaihoz JSON formátumban. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Saját DNS zóna virtuális hálózatra mutató hivatkozásának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Network/privateDnsZones/write | Hozzon létre vagy frissítsen egy saját DNS zónát az erőforráscsoporthoz. Vegye figyelembe, hogy ez a parancs nem használható virtuális hálózati kapcsolatok vagy rekordhalmazok zónán belüli létrehozására vagy frissítésére. |
> | Műveletek | Microsoft. Network/privateEndpointRedirectMaps/READ | Privát végpont RedirectMap beolvasása |
> | Műveletek | Microsoft. Network/privateEndpointRedirectMaps/Write | Privát végponti RedirectMap hoz létre vagy frissít egy meglévő privát végpont RedirectMap |
> | Műveletek | Microsoft.Network/privateEndpoints/delete | Egy privát végponti erőforrás törlése. |
> | Műveletek | Microsoft.Network/privateEndpoints/read | Egy privát végpont erőforrásának beolvasása. |
> | Műveletek | Microsoft.Network/privateEndpoints/write | Létrehoz egy új privát végpontot, vagy frissít egy meglévő privát végpontot. |
> | Műveletek | Microsoft.Network/privateLinkServices/delete | Egy privát link Service-erőforrás törlése. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Egy privát végponti kapcsolat definíciójának beolvasása. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Létrehoz egy új magánhálózati végponti kapcsolatokat, vagy frissíti a meglévő magánhálózati végponti kapcsolatokat. |
> | Műveletek | Microsoft.Network/privateLinkServices/read | Egy privát kapcsolati szolgáltatás erőforrásának beolvasása. |
> | Műveletek | Microsoft. Network/privateLinkServices/Write | Létrehoz egy új privát kapcsolati szolgáltatást, vagy frissíti a meglévő Private link Service-t. |
> | Műveletek | Microsoft.Network/publicIPAddresses/delete | Töröl egy nyilvános IP-címet. |
> | Műveletek | Microsoft.Network/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Műveletek | Microsoft.Network/publicIPAddresses/write | Létrehoz egy nyilvános IP-címet, vagy frissíti a meglévő nyilvános IP-címet.  |
> | Műveletek | Microsoft.Network/publicIPPrefixes/delete | Nyilvános IP-előtag törlése |
> | Műveletek | Microsoft.Network/publicIPPrefixes/join/action | Egy PublicIPPrefix illesztése. Nem riasztásos. |
> | Műveletek | Microsoft.Network/publicIPPrefixes/read | Nyilvános IP-előtag definíciójának beolvasása |
> | Műveletek | Microsoft.Network/publicIPPrefixes/write | Létrehoz egy nyilvános IP-előtagot, vagy frissíti a meglévő nyilvános IP-előtagot. |
> | Műveletek | Microsoft. Network/regisztráció/művelet | Regisztrálja az előfizetést |
> | Műveletek | Microsoft.Network/routeFilters/delete | Egy útvonal-szűrő definíciójának törlése |
> | Műveletek | Microsoft.Network/routeFilters/join/action | Egy útvonal-szűrőhöz csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/routeFilters/read | Egy útvonal-szűrő definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/delete | Egy útvonal-szűrő szabály definíciójának törlése |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/read | Egy útvonal-szűrő szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/write | Egy útvonal-szűrő szabály létrehozása vagy egy meglévő útvonal-szűrési szabály frissítése |
> | Műveletek | Microsoft. Network/routeFilters/Write | Útvonal-szűrő létrehozása vagy meglévő útvonal-szűrő frissítése |
> | Műveletek | Microsoft.Network/routeTables/delete | Útválasztási táblázat definíciójának törlése |
> | Műveletek | Microsoft. Network/routeTables/csatlakozás/művelet | Egy útválasztási táblázathoz csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/routeTables/READ | Útválasztási táblázat definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeTables/routes/delete | Útvonal-definíció törlése |
> | Műveletek | Microsoft. Network/routeTables/Routes/READ | Útvonal-definíció beolvasása |
> | Műveletek | Microsoft. Network/routeTables/Routes/Write | Útvonal létrehozása vagy meglévő útvonal frissítése |
> | Műveletek | Microsoft. Network/routeTables/Write | Útválasztási táblázat létrehozása vagy meglévő útválasztási táblázat frissítése |
> | Műveletek | Microsoft.Network/securegateways/delete | Biztonságos átjáró törlése |
> | Műveletek | Microsoft. Network/securegateways/READ | Biztonságos átjáró beszerzése |
> | Műveletek | Microsoft. Network/securegateways/Write | Biztonságos átjáró létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/delete | Szolgáltatás-végponti házirend törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/join/action | Csatlakozik egy szolgáltatás-végponti házirendhez. Nem riasztásos. |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Összekapcsolja az alhálózatot a szolgáltatás végponti házirendjeivel. Nem riasztásos. |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/read | Szolgáltatás-végponti szabályzat leírásának beolvasása |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Szolgáltatás-végponti házirend definíciójának törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Lekérdezi a szolgáltatás végponti házirendjének definíciójának leírását |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Létrehoz egy szolgáltatás-végpont házirend-definícióját, vagy frissíti egy meglévő szolgáltatási végpont házirend-definícióját. |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/write | Létrehoz egy szolgáltatás-végponti házirendet, vagy frissíti a meglévő szolgáltatási végponti házirendet |
> | Műveletek | Microsoft.Network/trafficManagerGeographicHierarchies/read | Lekéri a földrajzi forgalom útválasztási módszerével használható régiókat tartalmazó Traffic Manager földrajzi hierarchiát |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Töröl egy Azure-végpontot egy meglévő Traffic Manager-profilból. Traffic Manager leállítja az útválasztási forgalmat a törölt Azure-végpontra. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó Azure-végpontot, beleértve az adott Azure-végpont összes tulajdonságát. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adjon hozzá egy új Azure-végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő Azure-végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/delete | Törölje a Traffic Manager profilt. A Traffic Manager profiljához társított összes beállítás elvész, és a profil már nem használható a forgalom irányítására. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager megszakítja a törölt külső végpont felé irányuló útválasztási forgalmat. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó külső végpontot, amely tartalmazza az adott külső végpont összes tulajdonságát. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adjon hozzá egy új külső végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő külső végpont tulajdonságait az adott Traffic Manager profilban. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Lekérdezi a megadott Traffic Manager-profilhoz tartozó Traffic Manager Heat térképet, amely tartalmazza a lekérdezések számát és a késési időt a hely és a forrás IP-címe alapján. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Beágyazott végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager leállítja a törölt beágyazott végpontra irányuló útválasztási forgalmat. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó beágyazott végpontot, amely tartalmazza az adott beágyazott végpont összes tulajdonságát. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adjon hozzá egy új beágyazott végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő beágyazott végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager profil konfigurációjának beolvasása. Ide tartozik a DNS-beállítások, a forgalmi útválasztási beállítások, a végpont-figyelési beállítások, valamint a Traffic Manager profil által irányított végpontok listája. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/write | Hozzon létre egy Traffic Manager profilt, vagy módosítsa egy meglévő Traffic Manager-profil konfigurációját.<br>Ide tartozik a profilok engedélyezése vagy letiltása, valamint a DNS-beállítások, a forgalmi útválasztási beállítások vagy a végpont-figyelési beállítások módosítása.<br>A Traffic Manager profil által irányított végpontok hozzáadhatók, eltávolíthatók, engedélyezhetők vagy letilthatók. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Törli a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/read | Lekérdezi a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/write | Létrehoz egy új előfizetési szintű kulcsot, amelyet a rendszer a valós idejű felhasználói metrikák gyűjteményéhez használ. |
> | Műveletek | Microsoft.Network/unregister/action | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.Network/virtualHubs/delete | Virtuális központ törlése |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Deletes a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/virtualHubs/read | Virtuális központ beszerzése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/delete | VirtualHubRouteTableV2 törlése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/READ | VirtualHubRouteTableV2 beszerzése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/Write | VirtualHubRouteTableV2 létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualHubs/Write | Virtuális központ létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/Connections/READ | Get VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/delete | VirtualNetworkGateway törlése |
> | Műveletek | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Ügyfele-csomag előállítása a virtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VpnProfile-csomag előállítása a VirtualNetworkGateway |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Beolvassa a virtualNetworkGateway meghirdetett útvonalakat |
> | Műveletek | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | VirtualNetworkGateway BGP-társ állapotának beolvasása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/Action | Virtualnetworkgateway megtanult útvonalak beolvasása |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VPN-ügyfél kapcsolati állapotának beolvasása a VirtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | A VirtualNetworkGateway P2S-ügyfél ügyfele IPSec-paramétereinek beolvasása. |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Egy előre generált VPN Client profil csomag URL-címének beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway beolvasása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/reset/Action | VirtualNetworkGateway alaphelyzetbe állítása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | A VirtualNetworkGateway P2S-ügyfél ügyfele-alapú megosztott kulcsának alaphelyzetbe állítása. |
> | Műveletek | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ügyfele IPSec-paraméterek beállítása a VirtualNetworkGateway P2S-ügyfél számára. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | A támogatott VPN-eszközök listája |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualNetworks/BastionHosts/Action | Beolvas egy Virtual Network. |
> | Műveletek | Microsoft. Network/virtualNetworks/bastionHosts/default/művelet | Beolvas egy Virtual Network. |
> | Műveletek | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ellenőrizze, hogy az IP-cím elérhető-e a megadott virtuális hálózaton |
> | Műveletek | Microsoft.Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/virtualNetworks/peer/Action | Virtuális hálózat társítása egy másik virtuális hálózattal |
> | Műveletek | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/delete | Környezetfüggő szolgáltatásbeli végponti szabályzat törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/READ | Környezetfüggő szolgáltatási végponti szabályzatok beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózat/contextualServiceEndpointPolicies/írás | Környezetfüggő szolgáltatási végponti szabályzatot hoz létre, vagy frissíti a meglévő kontextus-szolgáltatás végponti szabályzatát |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/delete | Virtuális hálózati alhálózat törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Alhálózat előkészítése a szükséges hálózati házirendek alkalmazásával |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/művelet | Alhálózat előkészítésének megszüntetése az alkalmazott hálózati házirendek eltávolításával |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | A virtuális hálózati alhálózat összes virtuális gépre mutató hivatkozás beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/write | Létrehoz egy virtuális hálózati alhálózatot, vagy frissít egy meglévő virtuális hálózati alhálózatot |
> | Műveletek | Microsoft. Network/virtualNetworks/használat/olvasás | A virtuális hálózat minden alhálózatához tartozó IP-használat beszerzése |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualMachines/read | A virtuális hálózatban lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Virtuális hálózati társak törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/READ | Virtuális hálózat társítási definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Létrehoz egy virtuális hálózatot, vagy frissíti egy meglévő virtuális hálózati társat |
> | Műveletek | Microsoft.Network/virtualNetworks/write | Virtuális hálózat létrehozása vagy meglévő virtuális hálózat frissítése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/delete | Törlés Virtual Network koppintson |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/read | Virtual Network koppintás beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/write | Virtual Network létrehozása vagy frissítése koppintson |
> | Műveletek | Microsoft. Network/virtualRouters/delete | VirtualRouter törlése |
> | Műveletek | Microsoft. Network/virtualRouters/csatlakozás/művelet | Egy VirtualRouter illesztése. Nem riasztásos. |
> | Műveletek | Microsoft. Network/virtualRouters/READ | VirtualRouter beolvasása |
> | Műveletek | Microsoft. Network/virtualRouters/virtualRouterPeerings/delete | VirtualRouterPeering törlése |
> | Műveletek | Microsoft. Network/virtualRouters/virtualRouterPeerings/READ | VirtualRouterPeering beolvasása |
> | Műveletek | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Létrehoz egy VirtualRouterPeering, vagy frissít egy meglévő VirtualRouterPeering |
> | Műveletek | Microsoft. Network/virtualRouters/Write | Létrehoz egy VirtualRouter, vagy frissít egy meglévő VirtualRouter |
> | Műveletek | Microsoft.Network/virtualWans/delete | Virtuális WAN törlése |
> | Műveletek | Microsoft. Network/virtualwans/generateVpnProfile/Action | VirtualWanVpnServerConfiguration-VpnProfile előállítása |
> | Műveletek | Microsoft. Network/virtualWans/READ | Virtuális WAN beszerzése |
> | Műveletek | Microsoft. Network/virtualwans/supportedSecurityProviders/READ | A VirtualWan biztonsági szolgáltatók támogatottak. |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubs/read | Minden virtuális WAN-ra hivatkozó virtuális hubok beolvasása. |
> | Műveletek | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN-konfiguráció beolvasása |
> | Műveletek | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | VirtualWanVpnServerConfigurations beolvasása |
> | Műveletek | Microsoft.Network/virtualWans/vpnSites/read | A virtuális WAN-ra hivatkozó összes VPN-hely beolvasása. |
> | Műveletek | Microsoft. Network/virtualWans/Write | Virtuális WAN létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/vpnGateways/delete | Töröl egy átjáróban. |
> | Műveletek | microsoft.network/vpngateways/listvpnconnectionshealth/action | A kapcsolati állapot beolvasása a átjáróban lévő kapcsolatok teljes vagy egy részhalmazára |
> | Műveletek | Microsoft. Network/vpnGateways/READ | Átjáróban beolvasása. |
> | Műveletek | microsoft.network/vpngateways/reset/action | Átjáróban alaphelyzetbe állítása |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/delete | Töröl egy VpnConnection. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection beolvasása. |
> | Műveletek | Microsoft. Network/vpnGateways/VPN/vpnLinkConnections/READ | VPN-kapcsolat kapcsolatának beolvasása |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/write | Egy VpnConnection helyez el. |
> | Műveletek | Microsoft.Network/vpnGateways/write | Egy átjáróban helyez el. |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/delete | VpnServerConfiguration törlése |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/READ | VpnServerConfiguration beolvasása |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/Write | VpnServerConfiguration létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/vpnsites/delete | Törli a VPN-hely erőforrását. |
> | Műveletek | Microsoft.Network/vpnsites/read | Egy VPN-hely erőforrásának beolvasása. |
> | Műveletek | Microsoft. Network/vpnSites/vpnSiteLinks/READ | VPN-hely hivatkozásának beolvasása |
> | Műveletek | Microsoft.Network/vpnsites/write | Egy VPN-hely erőforrásának létrehozása vagy frissítése. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Ellenőrzi, hogy a megadott névtér-erőforrás neve elérhető-e a NotificationHub szolgáltatásban. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | A névtér-engedélyezési szabály újragenerálta az elsődleges/Értesítésiközpont, megadhatja azt a kulcsot, amelyet újra kell generált |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Ellenőrzi, hogy a megadott NotificationHub-név elérhető-e a névtéren belül. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/Delete | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/művelet | Az értesítési központ engedélyezési szabályainak listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Értesítési központ engedélyezési szabályainak törlése |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Az értesítési központ kapcsolódási karakterláncának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/READ | Az értesítési központ engedélyezési szabályainak listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Az értesítési központ engedélyezési szabálya újragenerálta az elsődleges/Értesítésiközpont, megadhatja azt a kulcsot, amelyet újra kell generált |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/Write | Hozzon létre értesítési központ engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Teszt leküldéses értesítés küldése. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Értesítési központ erőforrásának törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Az összes értesítési központ PNS hitelesítő adatainak beolvasása. Ide tartozik a WNS, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatai. |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/olvasás | Értesítési központ erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/írás | Hozzon létre egy értesítési központot, és frissítse a tulajdonságait. A tulajdonságai főleg PNS hitelesítő adatokkal rendelkeznek. Engedélyezési szabályok és TTL |
> | Műveletek | Microsoft. NotificationHubs/névterek/olvasás | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft.NotificationHubs/operationResults/read | Notification Hubs szolgáltató műveleti eredményének visszaadása |
> | Műveletek | Microsoft.NotificationHubs/operations/read | Notification Hubs szolgáltató által támogatott műveletek listáját adja vissza. |
> | Műveletek | Microsoft.NotificationHubs/register/action | Regisztrálja az előfizetést a NotificationHubs erőforrás-szolgáltatóhoz, és lehetővé teszi a névterek és NotificationHubs létrehozását |
> | Műveletek | Microsoft.NotificationHubs/unregister/action | A NotificationHubs erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése, és a névterek és NotificationHubs létrehozásának engedélyezése |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. OffAzure/HyperVSites/fürtök/olvasás | Egy Hyper-V fürt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/fürtök/írás | A Hyper-V fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/delete | A Hyper-V hely törlése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/healthsummary/READ | A Hyper-V erőforrás állapot-összefoglalásának beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/read | Egy Hyper-V-gazdagép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/write | A Hyper-V-gazdagép létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/feladatok/olvasás | A Hyper-V-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/Machines/READ | A Hyper-V gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/operationsstatus/READ | Egy Hyper-V műveleti állapot tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/READ | Egy Hyper-V-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/frissítés/művelet | A Hyper-V-helyen lévő objektumok frissítése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/használat/olvasás | Egy Hyper-V webhely használatának beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/Write | A Hyper-V-hely létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/Operations/READ | Beolvassa a feltett műveleteket |
> | Műveletek | Microsoft.OffAzure/register/action | Előfizetés regisztrálása a Microsoft. OffAzure erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. OffAzure/ServerSites/feladatok/olvasás | Egy kiszolgálói feladat tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Machines/READ | A kiszolgálói gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Machines/Write | Kiszolgálói gépek tulajdonságainak megírása |
> | Műveletek | Microsoft. OffAzure/ServerSites/operationsstatus/READ | Egy kiszolgáló műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/READ | Egy kiszolgáló-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/frissítés/művelet | Az objektumok frissítése egy kiszolgálói helyen belül |
> | Műveletek | Microsoft. OffAzure/ServerSites/runasaccounts/READ | A kiszolgálói futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/használat/olvasás | Egy kiszolgálói webhely használatának beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Write | A kiszolgáló helyének létrehozása vagy frissítése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/delete | A VMware-hely törlése |
> | Műveletek | Microsoft. OffAzure/VMwareSites/healthsummary/READ | A VMware-erőforrás állapot-összefoglalásának beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/jobs/read | VMware-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/read | VMware-gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware-gépek elindítása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/stop/action | Leállítja a VMware-gépeket |
> | Műveletek | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Egy VMware-műveleti állapot tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/read | Egy VMware-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/refresh/action | Egy VMware-helyen található objektumok frissítése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/usage/read | VMware-webhely használatának beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/read | Egy VMware-vCenter tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/write | A VMware-vCenter létrehozása vagy frissítése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/write | A VMware-hely létrehozása vagy frissítése |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationalInsights/linkTargets/read | Felsorolja azokat a meglévő fiókokat, amelyek nincsenek Azure-előfizetéshez társítva. Ha ezt az Azure-előfizetést egy munkaterülethez szeretné kapcsolni, használja a művelet által visszaadott ügyfél-azonosítót a munkaterület létrehozása művelet ügyfél-azonosító tulajdonságában. |
> | Műveletek | microsoft.operationalinsights/operations/read | Felsorolja az összes rendelkezésre álló OperationalInsights REST API-műveletet. |
> | Műveletek | microsoft.operationalinsights/register/action | Rergisters az előfizetést. |
> | Műveletek | Microsoft.OperationalInsights/register/action | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Műveletek | microsoft.operationalinsights/unregister/action | Az előfizetés regisztrációjának törlése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/művelet | Keresés az új motor használatával. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Konfigurációs hatókör törlése |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Konfiguráció hatókörének beolvasása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Konfigurációs hatókör beállítása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/delete | Munkaterületen lévő adatforrások törlése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/adatforrások/írás | Adatforrások létrehozása/frissítése egy munkaterület alatt. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/törlés | Töröl egy munkaterületet. Ha a munkaterületet egy meglévő munkaterülethez csatolták a létrehozáskor, akkor az ahhoz csatolt munkaterületet nem törli a rendszer. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/átjárók/törlés | Eltávolítja a munkaterülethez konfigurált átjárót. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/generateregistrationcertificate/művelet | Létrehoz egy regisztrációs tanúsítványt a munkaterülethez. Ez a tanúsítvány a Microsoft System Center Operation Manager és a munkaterület összekapcsolására szolgál. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Letiltja az adott munkaterülethez tartozó intelligencia-csomagokat. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Egy adott munkaterülethez tartozó intelligencia-csomag engedélyezése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Felsorolja az adott munkaterület számára látható összes intelligenciát, valamint azt is, hogy a csomag engedélyezve van-e vagy le van-e tiltva az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Társított szolgáltatások törlése az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/linkedServices/olvasás | Társított szolgáltatások beolvasása az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/linkedServices/írás | Társított szolgáltatások létrehozása/frissítése az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/művelet | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/olvasás | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/managementGroups/olvasás | Beolvassa a munkaterülethez kapcsolódó System Center Operations Manager felügyeleti csoportok nevét és metaadatait. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/metricDefinitions/olvasás | Metrika-definíciók beolvasása a munkaterület területen |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | A felhasználó értesítési beállításainak törlése a munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/notificationSettings/olvasás | A felhasználó értesítési beállításainak beolvasása a munkaterülethez. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/notificationSettings/írás | Adja meg a felhasználó értesítési beállításait a munkaterülethez. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/műveletek/olvasás | Egy OperationalInsights-munkaterület művelet állapotának beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesAccountLogon/olvasás | Adatok beolvasása a AADDomainServicesAccountLogon táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Adatok beolvasása a AADDomainServicesAccountManagement táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Adatok beolvasása a AADDomainServicesDirectoryServiceAccess táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesLogonLogoff/olvasás | Adatok beolvasása a AADDomainServicesLogonLogoff táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesPolicyChange/olvasás | Adatok beolvasása a AADDomainServicesPolicyChange táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Adatok beolvasása a AADDomainServicesPrivilegeUse táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Adatok beolvasása a AADDomainServicesSystemSecurity táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Adatok beolvasása a ADAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupAlerts/olvasás | Adatok beolvasása a AddonAzureBackupAlerts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupJobs/olvasás | Adatok beolvasása a AddonAzureBackupJobs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupPolicy/olvasás | Adatok beolvasása a AddonAzureBackupPolicy táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupProtectedInstance/olvasás | Adatok beolvasása a AddonAzureBackupProtectedInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupStorage/olvasás | Adatok beolvasása a AddonAzureBackupStorage táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Adatok beolvasása a ADFActivityRun táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Adatok beolvasása a ADFPipelineRun táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Adatok beolvasása a ADFTriggerRun táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Adatok beolvasása a ADReplicationResult táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Adatok beolvasása a ADSecurityAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Alert/read | Adatok beolvasása a riasztási táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Adatok beolvasása a AlertHistory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeClusterEvent/olvasás | Adatok beolvasása a AmlComputeClusterEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeClusterNodeEvent/olvasás | Adatok beolvasása a AmlComputeClusterNodeEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeJobEvent/olvasás | Adatok beolvasása a AmlComputeJobEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ApiManagementGatewayLogs/olvasás | Adatok beolvasása a ApiManagementGatewayLogs táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Adatok beolvasása a AppCenterError táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Adatok beolvasása a ApplicationInsights táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppPlatformLogsforSpring/olvasás | Adatok beolvasása a AppPlatformLogsforSpring táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceEnvironmentPlatformLogs/olvasás | Adatok beolvasása a AppServiceEnvironmentPlatformLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AuditLogs/olvasás | Adatok beolvasása a AuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AutoscaleEvaluationsLog/olvasás | Adatok beolvasása a AutoscaleEvaluationsLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Adatok beolvasása a AutoscaleScaleActionsLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Adatok beolvasása a AWSCloudTrail táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Adatok beolvasása a AzureActivity táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Adatok beolvasása a AzureAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Adatok beolvasása a AzureMetrics táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiClusterEvent/olvasás | Adatok beolvasása a BaiClusterEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiClusterNodeEvent/olvasás | Adatok beolvasása a BaiClusterNodeEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiJobEvent/olvasás | Adatok beolvasása a BaiJobEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainApplicationLog/olvasás | Adatok beolvasása a BlockchainApplicationLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainProxyLog/olvasás | Adatok beolvasása a BlockchainProxyLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Adatok beolvasása a BoundPort táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Adatok beolvasása a CommonSecurityLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Adatok beolvasása a ComputerGroup táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/konfigurációváltozás/olvasás | Adatok beolvasása a konfigurációváltozás táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Adatok beolvasása a ConfigurationData táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Adatok beolvasása a ContainerImageInventory táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Adatok beolvasása a ContainerInventory táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Adatok beolvasása a ContainerLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Adatok beolvasása a ContainerNodeInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerRegistryLoginEvents/olvasás | Adatok beolvasása a ContainerRegistryLoginEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerRegistryRepositoryEvents/olvasás | Adatok beolvasása a ContainerRegistryRepositoryEvents táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Adatok beolvasása a ContainerServiceLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/CoreAzureBackup/olvasás | Adatok beolvasása a CoreAzureBackup táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksAccounts/olvasás | Adatok beolvasása a DatabricksAccounts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksClusters/olvasás | Adatok beolvasása a DatabricksClusters táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksDBFS/olvasás | Adatok beolvasása a DatabricksDBFS táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksInstancePools/olvasás | Adatok beolvasása a DatabricksInstancePools táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksJobs/olvasás | Adatok beolvasása a DatabricksJobs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksNotebook/olvasás | Adatok beolvasása a DatabricksNotebook táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSecrets/olvasás | Adatok beolvasása a DatabricksSecrets táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Adatok beolvasása a DatabricksSQLPermissions táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSSH/olvasás | Adatok beolvasása a DatabricksSSH táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksTables/olvasás | Adatok beolvasása a DatabricksTables táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksWorkspace/olvasás | Adatok beolvasása a DatabricksWorkspace táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Adatok beolvasása a DeviceAppCrash táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Adatok beolvasása a DeviceAppLaunch táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCalendar/olvasás | Adatok beolvasása a DeviceCalendar táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCleanup/olvasás | Adatok beolvasása a DeviceCleanup táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Adatok beolvasása a DeviceConnectSession táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceEtw/olvasás | Adatok beolvasása a DeviceEtw táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Adatok beolvasása a DeviceHardwareHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Adatok beolvasása a DeviceHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceHeartbeat/olvasás | Adatok beolvasása a DeviceHeartbeat táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSkypeHeartbeat/olvasás | Adatok beolvasása a DeviceSkypeHeartbeat táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Adatok beolvasása a DeviceSkypeSignIn táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSleepState/olvasás | Adatok beolvasása a DeviceSleepState táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHAppFailure/olvasás | Adatok beolvasása a DHAppFailure táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Adatok beolvasása a DHAppReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHDriverReliability/olvasás | Adatok beolvasása a DHDriverReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonFailures/olvasás | Adatok beolvasása a DHLogonFailures táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonMetrics/olvasás | Adatok beolvasása a DHLogonMetrics táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSCrashData/olvasás | Adatok beolvasása a DHOSCrashData táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSReliability/olvasás | Adatok beolvasása a DHOSReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHWipAppLearning/olvasás | Adatok beolvasása a DHWipAppLearning táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsEvents/olvasás | Adatok beolvasása a DnsEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsInventory/olvasás | Adatok beolvasása a DnsInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ETWEvent/olvasás | Adatok beolvasása a ETWEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/esemény/olvasás | Adatok beolvasása az Event táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Adatok beolvasása a ExchangeAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Adatok beolvasása a ExchangeOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/FailedIngestion/olvasás | Adatok beolvasása a FailedIngestion táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/szívverés/olvasás | Adatok beolvasása a szívverési táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/HuntingBookmark/olvasás | Adatok beolvasása a HuntingBookmark táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Adatok beolvasása a IISAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Adatok beolvasása a InboundConnection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/InsightsMetrics/olvasás | Adatok beolvasása a InsightsMetrics táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Adatok beolvasása a IntuneAuditLogs táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Adatok beolvasása a IntuneOperationalLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeEvents/olvasás | Adatok beolvasása a KubeEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeHealth/olvasás | Adatok beolvasása a KubeHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeNodeInventory/olvasás | Adatok beolvasása a KubeNodeInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubePodInventory/olvasás | Adatok beolvasása a KubePodInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeServices/olvasás | Adatok beolvasása a KubeServices táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/LinuxAuditLog/olvasás | Adatok beolvasása a LinuxAuditLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplication/olvasás | Adatok beolvasása a MAApplication táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Adatok beolvasása a MAApplicationHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Adatok beolvasása a MAApplicationHealthAlternativeVersions táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Adatok beolvasása a MAApplicationHealthIssues táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Adatok beolvasása a MAApplicationInstance táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Adatok beolvasása a MAApplicationInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationReadiness/olvasás | Adatok beolvasása a MAApplicationReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeploymentPlan/olvasás | Adatok beolvasása a MADeploymentPlan táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevice/olvasás | Adatok beolvasása a MADevice táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceNotEnrolled/olvasás | Adatok beolvasása a MADeviceNotEnrolled táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Adatok beolvasása a MADeviceNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Adatok beolvasása a MADevicePnPHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Adatok beolvasása a MADevicePnPHealthAlternativeVersions táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Adatok beolvasása a MADevicePnPHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceReadiness/olvasás | Adatok beolvasása a MADeviceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverInstanceReadiness/olvasás | Adatok beolvasása a MADriverInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverReadiness/olvasás | Adatok beolvasása a MADriverReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddin/olvasás | Adatok beolvasása a MAOfficeAddin táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Adatok beolvasása a MAOfficeAddinEntityHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Adatok beolvasása a MAOfficeAddinHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Adatok beolvasása a MAOfficeAddinHealthEventNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Adatok beolvasása a MAOfficeAddinHealthIssues táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Adatok beolvasása a MAOfficeAddinInstance táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Adatok beolvasása a MAOfficeAddinInstanceReadiness táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Adatok beolvasása a MAOfficeAddinReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeApp/olvasás | Adatok beolvasása a MAOfficeApp táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Adatok beolvasása a MAOfficeAppCrashesNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Adatok beolvasása a MAOfficeAppHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Adatok beolvasása a MAOfficeAppInstance táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Adatok beolvasása a MAOfficeAppInstanceHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Adatok beolvasása a MAOfficeAppReadiness táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Adatok beolvasása a MAOfficeAppSessionsNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Adatok beolvasása a MAOfficeBuildInfo táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessment/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessment táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Adatok beolvasása a MAOfficeDeploymentStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Adatok beolvasása a MAOfficeDeploymentStatusNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Adatok beolvasása a MAOfficeMacroErrorNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroGlobalHealth/olvasás | Adatok beolvasása a MAOfficeMacroGlobalHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroHealth/olvasás | Adatok beolvasása a MAOfficeMacroHealth táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Adatok beolvasása a MAOfficeMacroHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueInstanceReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroSummary/olvasás | Adatok beolvasása a MAOfficeMacroSummary táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeSuite/olvasás | Adatok beolvasása a MAOfficeSuite táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Adatok beolvasása a MAOfficeSuiteInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAProposedPilotDevices/olvasás | Adatok beolvasása a MAProposedPilotDevices táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Adatok beolvasása a MAWindowsBuildInfo táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessment/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessment táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Adatok beolvasása a MAWindowsDeploymentStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Adatok beolvasása a MAWindowsDeploymentStatusNRT táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Adatok beolvasása a MAWindowsSysReqInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/McasShadowItReporting/olvasás | Adatok beolvasása a McasShadowItReporting táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDataShareShareLog/olvasás | Adatok beolvasása a MicrosoftDataShareShareLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftHealthcareApisAuditLogs/olvasás | Adatok beolvasása a MicrosoftHealthcareApisAuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftInsightsAzureActivityLog/olvasás | Adatok beolvasása a MicrosoftInsightsAzureActivityLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebApplicationLog/olvasás | Adatok beolvasása a MicrosoftWebApplicationLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebFunctionExecutionLogs/olvasás | Adatok beolvasása a MicrosoftWebFunctionExecutionLogs táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Adatok beolvasása a MicrosoftWebStdOutStdErrLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebW3CLog/olvasás | Adatok beolvasása a MicrosoftWebW3CLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/NetworkMonitoring/olvasás | Adatok beolvasása a NetworkMonitoring táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OfficeActivity/olvasás | Adatok beolvasása a OfficeActivity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/művelet/olvasás | Adatok beolvasása a műveleti táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OutboundConnection/olvasás | Adatok beolvasása a OutboundConnection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/Perf/olvasás | Adatok beolvasása a Perf táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Adatok beolvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Adatok beolvasása a ReservedAzureCommonFields táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Adatok beolvasása a ReservedCommonFields táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Adatok beolvasása a SCCMAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Adatok beolvasása a SCOMAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Adatok beolvasása a SecurityAlert táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Adatok beolvasása a SecurityBaseline táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Adatok beolvasása a SecurityBaselineSummary táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Adatok beolvasása a SecurityDetection táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Adatok beolvasása a SecurityEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Adatok beolvasása a SecurityIoTRawEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Adatok beolvasása a SecurityRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ServiceFabricOperationalEvent/olvasás | Adatok beolvasása a ServiceFabricOperationalEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Adatok beolvasása a ServiceFabricReliableActorEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Adatok beolvasása a ServiceFabricReliableServiceEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Adatok beolvasása a SfBAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Adatok beolvasása a SfBOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Adatok beolvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Adatok beolvasása a SigninLogs táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Adatok beolvasása a SPAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Adatok beolvasása a SQLAssessmentRecommendation táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Adatok beolvasása a SQLQueryPerformance táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Adatok beolvasása a SqlThreatProtectionLoginAudits táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Adatok beolvasása a SqlVulnerabilityAssessmentResult táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SucceededIngestion/olvasás | Adatok beolvasása a SucceededIngestion táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/syslog/olvasás | Adatok beolvasása a syslog-táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SysmonEvent/olvasás | Adatok beolvasása a SysmonEvent táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Adatok olvasása bármely egyéni naplóból |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Adatok beolvasása a ThreatIntelligenceIndicator táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAApp/olvasás | Adatok beolvasása a UAApp táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputer/olvasás | Adatok beolvasása a UAComputer táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputerRank/olvasás | Adatok beolvasása a UAComputerRank táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriver/olvasás | Adatok beolvasása a UADriver táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriverProblemCodes/olvasás | Adatok beolvasása a UADriverProblemCodes táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAFeedback/olvasás | Adatok beolvasása a UAFeedback táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAHardwareSecurity/olvasás | Adatok beolvasása a UAHardwareSecurity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAIESiteDiscovery/olvasás | Adatok beolvasása a UAIESiteDiscovery táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Adatok beolvasása a UAOfficeAddIn táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAProposedActionPlan/olvasás | Adatok beolvasása a UAProposedActionPlan táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UASysReqIssue/olvasás | Adatok beolvasása a UASysReqIssue táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAUpgradedComputer/olvasás | Adatok beolvasása a UAUpgradedComputer táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/frissítés/olvasás | Adatok beolvasása a frissítési táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UpdateRunProgress/olvasás | Adatok beolvasása a UpdateRunProgress táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/updateSummary típusú/olvasás | Adatok beolvasása a updateSummary típusú táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/használat/olvasás | Adatok beolvasása a használati táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMBoundPort/olvasás | Adatok beolvasása a VMBoundPort táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMConnection/olvasás | Adatok beolvasása a VMConnection táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Adatok beolvasása a W3CIISLog táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Adatok beolvasása a WaaSDeploymentStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Adatok beolvasása a WaaSInsiderStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Adatok beolvasása a WaaSUpdateStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Adatok beolvasása a WDAVStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WDAVThreat/olvasás | Adatok beolvasása a WDAVThreat táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Adatok beolvasása a WindowsClientAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsEvent/olvasás | Adatok beolvasása a WindowsEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsFirewall/olvasás | Adatok beolvasása a WindowsFirewall táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Adatok beolvasása a WindowsServerAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WireData/olvasás | Adatok beolvasása a WireData táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Adatok beolvasása a WorkloadMonitoringPerf táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOAggregatedStatus/olvasás | Adatok beolvasása a WUDOAggregatedStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOStatus/olvasás | Adatok beolvasása a WUDOStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/read | Meglévő munkaterület beolvasása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/regeneratesharedkey/művelet | A megadott munkaterület megosztott kulcsának újralétrehozása |
> | Műveletek | microsoft.operationalinsights/workspaces/rules/read | Az összes riasztási szabály beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Töröl egy mentett keresési lekérdezést |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/eredmények/olvasás | Mentett keresések eredményeinek beolvasása. Elavult |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/törlés | Ütemezett keresési műveletek törlése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/olvasás | Ütemezett keresési műveletek beolvasása. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/írás | Ütemezett keresési műveletek létrehozása vagy frissítése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/törlés | Ütemezett keresések törlése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/olvasás | Ütemezett keresések beolvasása. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemterv/írás | Ütemezett keresések létrehozása vagy frissítése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/savedSearches/írás | Mentett keresési lekérdezés létrehozása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/séma/olvasás | Lekéri a munkaterület keresési sémáját.  A keresési séma tartalmazza a kitett mezőket és azok típusait. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/keresés/olvasás | Keresési eredmények beolvasása. Elavult. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Törli a tárolási konfigurációt. Ezzel leállítja a Microsoft Operational Insights az adatoknak a Storage-fiókból való beolvasását. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/olvasás | Egy tárolási konfiguráció beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/írás | Új tárolási konfiguráció létrehozása. Ezek a konfigurációk egy meglévő Storage-fiókban lévő hely adatainak lekérésére szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/upgradetranslationfailures/olvasás | A keresés a munkaterületre vonatkozó verziófrissítési sikertelen fordítási naplójának beolvasása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/használati adatok/olvasás | Lekéri egy munkaterület használati adatait, beleértve a munkaterület által olvasott adatok mennyiségét. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/write | Létrehoz egy új munkaterületet vagy egy meglévő munkaterületre mutató hivatkozásokat úgy, hogy megadja az ügyfél azonosítóját a meglévő munkaterületen. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/delete | Meglévő felügyeleti társítás törlése |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/read | Meglévő felügyeleti társítás beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/write | Új felügyeleti társítás létrehozása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/delete | Meglévő felügyeleti konfiguráció törlése |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/read | Meglévő felügyeleti konfiguráció beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/write | Új felügyeleti konfiguráció létrehozása |
> | Műveletek | Microsoft.OperationsManagement/register/action | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft.OperationsManagement/solutions/delete | Meglévő OMS-megoldás törlése |
> | Műveletek | Microsoft.OperationsManagement/solutions/read | OMS-megoldás beolvasása |
> | Műveletek | Microsoft.OperationsManagement/solutions/write | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PolicyInsights/asyncOperationResults/read | Az aszinkron művelet eredményének beolvasása. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/művelet | Egy adott összetevő megfelelőségi állapotának ellenőrzése adatszabályzatok szerint. |
> | Műveletek | Microsoft.PolicyInsights/operations/read | A Microsoft. PolicyInsights névtérben támogatott műveletek beolvasása |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/művelet | Naplózza az erőforrás-összetevő házirendjének eseményeit. |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/action | A házirend eseményeivel kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/read | A házirend eseményeivel kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/queryResults/action | A házirend állapotával kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/queryResults/READ | A házirend állapotával kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/összefoglalás/művelet | A szabályzat legutóbbi állapotával kapcsolatos összegző információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/summarize/read | A szabályzat legutóbbi állapotával kapcsolatos összegző információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Új megfelelőségi értékelést indít a kiválasztott hatókörhöz. |
> | Műveletek | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | A DeployIfNotExists-szabályzatok által igényelt erőforrásokra vonatkozó információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/register/action | Regisztrálja a Microsoft Policy bepillantást biztosító erőforrás-szolgáltatót, és engedélyezi a műveleteket. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/megszakítás/művelet | A folyamatban lévő Microsoft-szabályzatok szervizelésének megszakítása. |
> | Műveletek | Microsoft.PolicyInsights/remediations/delete | Házirend-szervizelés törlése. |
> | Műveletek | Microsoft.PolicyInsights/remediations/listDeployments/read | Felsorolja a házirendek szervizeléséhez szükséges központi telepítéseket. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/olvasás | Házirend szervizelésének beolvasása. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/írás | Microsoft Policy szervizelések létrehozása vagy frissítése. |
> | Műveletek | Microsoft.PolicyInsights/unregister/action | A Microsoft Policy-erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Portal/konzolok/törlés | Eltávolítja a Cloud Shell példányt. |
> | Műveletek | Microsoft. Portal/konzolok/olvasás | A Cloud Shell példány beolvasása. |
> | Műveletek | Microsoft. Portal/konzolok/írás | Cloud Shell példány létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Portal/irányítópultok/törlés | Eltávolítja az irányítópultot az előfizetésből. |
> | Műveletek | Microsoft. Portal/irányítópultok/olvasás | Az előfizetés irányítópultjának beolvasása. |
> | Műveletek | Microsoft. Portal/irányítópultok/írás | Irányítópult hozzáadása vagy módosítása előfizetéshez. |
> | Műveletek | Microsoft.Portal/register/action | Regisztráció a portálra |
> | Műveletek | Microsoft.Portal/usersettings/delete | Eltávolítja a Cloud Shell felhasználói beállításait. |
> | Műveletek | Microsoft.Portal/usersettings/read | A Cloud Shell felhasználói beállításainak beolvasása. |
> | Műveletek | Microsoft. Portal/usersettings/írás | Cloud Shell felhasználói beállítás létrehozása vagy frissítése. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/delete | Törli a Power BI dedikált kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/read | A megadott Power BI dedikált kapacitás információinak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/resume/action | Folytatja a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/skus/read | Rendelkezésre álló SKU-információk beolvasása a kapacitáshoz |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/suspend/action | Felfüggeszti a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/write | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Power BI dedikált kapacitás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationresults/read | A megadott műveleti eredmény információinak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Lekéri a megadott műveleti állapot adatait. |
> | Műveletek | Microsoft.PowerBIDedicated/operations/read | A műveletek információinak lekérése |
> | Műveletek | Microsoft.PowerBIDedicated/register/action | Regisztrálja Power BI dedikált erőforrás-szolgáltatót. |
> | Műveletek | Microsoft.PowerBIDedicated/skus/read | A SKU-ra vonatkozó információk beolvasása |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.RecoveryServices/locations/allocatedStamp/read | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft.RecoveryServices/locations/allocateStamp/action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Védett elemek biztonsági másolatának létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Ellenőrizze, hogy az erőforrás neve rendelkezésre állása egy API-e, hogy elérhető-e az erőforrás neve |
> | Műveletek | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/register/action | Az adott erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services-tároló konfigurációjának visszaadása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/write | Recovery Services tár konfigurációjának frissítése. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Biztonsági másolat védelmi leképezésének törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági másolat védelmi szándékának beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationsStatus/READ | A művelet állapotának visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | A regisztrált tároló törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | A tárolóban lévő számítási feladatok lekérdezése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Tároló összes elemének beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/operationsStatus/READ | Lekéri a védelmi tárolón végrehajtott művelet állapotát. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | A védett elemek biztonsági mentését végzi. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Védett elemek törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | A védett elemek helyreállítási pontjainak beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Védett elemek biztonsági másolatának létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Létrehoz egy regisztrált tárolót |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Feladat megszakítása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatok műveletének eredményét adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/operationsStatus/READ | A feladatok műveletének állapotát adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/read | Az összes feladatütemezés visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services-tár biztonsági mentési műveletének eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Védelmi szabályzat törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A házirend-művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi szabályzat létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Az összes védhető elem listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Recovery Services-tároló biztonsági PIN-kódjának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Frissíti Recovery Services tár tárolási konfigurációját. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A művelet érvényesítése védett elemen |
> | Műveletek | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/delete | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | A Recovery Services-tár értesítési konfigurációjának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | E-mail-értesítések konfigurálása a Recovery Services-tárolóhoz. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A tároló regisztrációjának törlése művelettel törölheti a tároló regisztrációját. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Riasztási beállítások beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Riasztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | A háló konzisztenciájának ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Bármilyen háló törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | A Process Server lemezképének üzembe helyezése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Háló migrálása a HRE-be |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-hálón |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Bármilyen háló beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újrahozzárendelése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Háló eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Bármely logikai hálózat beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Hálózati hozzárendelések törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Hálózati leképezések létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Védhető elemek felderítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-védelmi tárolókban |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Védelmi tároló eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Az áttelepítési elemek törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Elemek átmigrálása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Áttelepítési helyreállítási pontok olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-áttelepítési elemeken |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Bármely áttelepítési elem olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Áttelepítés tesztelése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Karbantartás áttelepíthetők |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Áttelepítési elemek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elem olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lemezek hozzáadása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Védett elemek törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás által védett elemeken |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Védett elemek eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Lemezek eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elemek ismételt védelme |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Visszajelzés küldése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Bármely cél számítási méret olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Feladatátvételi teszt karbantartása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Védett elemek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | A védelmi tárolók összes leképezésének törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/READ | Egy aszinkron művelet eredményének nyomon követése az erőforrás-védelmi tároló-hozzárendeléseken |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Védelmi tároló megfeleltetésének eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Védelmi tárolók hozzárendeléseinek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Védelmi tároló váltása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Védelmi tárolók létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services szolgáltatók törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-Recovery Services szolgáltatókon |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services-szolgáltató eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services szolgáltatók létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | A tárolási besorolási leképezések törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-tároló besorolási hozzárendelésein |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Tárolási besorolási leképezések létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Tetszőleges vCenter törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/operationresults/READ | Egy aszinkron művelet eredményének nyomon követése az erőforrás vCenter |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Tetszőleges vCenter létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Bármilyen háló létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Feladat megszakítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-feladatokban |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/read | Bármilyen feladat beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Újraindítási feladatok |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Feladatok folytatása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Bármely áttelepítési elem olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Bármely szabályzat törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-házirendekben |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Bármilyen szabályzat beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Szabályzatok létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Védett elemek beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Helyreállítási tervek törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvételi véglegesítés helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-helyreállítási tervekben |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Helyreállítási terv ismételt védetté |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi teszt helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Helyreállítási tervek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Bármilyen beolvasás  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationUsages/read | A tár replikációs használatának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationVaultHealth/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-tároló replikációs állapotában |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | A tár replikációs állapotának olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Tár állapotának frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Bármilyen beolvasás  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Tetszőlegesek létrehozása vagy frissítése  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Bármely tár használatának olvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Relay/checkNameAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft.Relay/checkNamespaceAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/művelet | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft.Relay/namespaces/Delete | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. Relay/névtér/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/művelet | A HybridConnection frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/delete | HybridConnection-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A HybridConnection tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/READ |  A HybridConnection-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/írás | Hozzon létre HybridConnection engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névtér/messagingPlan/olvasás | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névterek/messagingPlan/írás | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névtér/operationresults/olvasás | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/read | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/művelet | A WcfRelay frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/delete | WcfRelay-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A WcfRelay tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/READ |  A WcfRelay-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/regeneratekeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/írás | Hozzon létre WcfRelay engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/delete | WcfRelay-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/olvasás | WcfRelay-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/írás | WcfRelay tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft.Relay/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.Relay/register/action | Regisztrálja az előfizetést a továbbító erőforrás-szolgáltató számára, és lehetővé teszi a továbbítási erőforrások létrehozását |
> | Műveletek | Microsoft.Relay/unregister/action | Regisztrálja az előfizetést a továbbító erőforrás-szolgáltató számára, és lehetővé teszi a továbbítási erőforrások létrehozását |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Lekéri a megadott erőforrás rendelkezésre állási állapotát. |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Műveletek | Microsoft.ResourceHealth/events/read | Az adott előfizetés Service Health eseményeinek beolvasása |
> | Műveletek | Microsoft.Resourcehealth/healthevent/action | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Activated/action | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft.Resourcehealth/healthevent/InProgress/action | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Pending/action | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/megoldott/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Updated/action | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft.ResourceHealth/impactedResources/read | Érintett erőforrások beolvasása az adott előfizetéshez |
> | Műveletek | Microsoft.ResourceHealth/metadata/read | Metaadatok beolvasása |
> | Műveletek | Microsoft.ResourceHealth/Operations/read | A Microsoft ResourceHealth elérhető műveletek beolvasása |
> | Műveletek | Microsoft.ResourceHealth/register/action | Regisztrálja az előfizetést a Microsoft ResourceHealth |
> | Műveletek | Microsoft.ResourceHealth/unregister/action | A Microsoft ResourceHealth-előfizetés regisztrációjának törlése |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Resources/calculateTemplateHash/Action | Kiszámítja a megadott sablon kivonatát. |
> | Műveletek | Microsoft.Resources/checkPolicyCompliance/action | Egy adott erőforrás megfelelőségi állapotának ellenőrzése erőforrás-házirendekkel. |
> | Műveletek | Microsoft.Resources/checkResourceName/action | Győződjön meg arról, hogy az erőforrás neve érvényes. |
> | Műveletek | Microsoft. Resources/központi telepítések/megszakítás/művelet | Egy központi telepítés megszakítása. |
> | Műveletek | Microsoft.Resources/deployments/delete | Törli a központi telepítést. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/exportTemplate/művelet | Sablon exportálása központi telepítéshez |
> | Műveletek | Microsoft.Resources/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/operationstatuses/olvasás | Lekérdezi vagy felsorolja a telepítési művelet állapotát. |
> | Műveletek | Microsoft.Resources/deployments/read | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/deployments/validate/action | Érvényesíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/deployments/whatIf/action | Megjósolja a sablon központi telepítésének változásait. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/írás | Egy központi telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Resources/links/delete | Töröl egy erőforrás-hivatkozást. |
> | Műveletek | Microsoft.Resources/links/read | Lekérdezi vagy felsorolja az erőforrás-hivatkozásokat. |
> | Műveletek | Microsoft. Resources/Links/Write | Létrehoz vagy frissít egy erőforrás-hivatkozást. |
> | Műveletek | Microsoft. Resources/Marketplace/vásárlás/művelet | Vásárol egy erőforrást a piactéren. |
> | Műveletek | Microsoft. Resources/Providers/READ | A szolgáltatók listájának beolvasása. |
> | Műveletek | Microsoft.Resources/resources/read | Az erőforrások listájának lekérése szűrők alapján. |
> | Műveletek | Microsoft. Resources/Subscriptions/Locations/READ | A támogatott helyszínek listájának beolvasása. |
> | Műveletek | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Resources/előfizetések/szolgáltatók/olvasás | Lekérdezi vagy felsorolja az erőforrás-szolgáltatókat. |
> | Műveletek | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/delete | Töröl egy erőforráscsoportot és annak összes erőforrását. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Lekérdezi vagy felsorolja a telepítési művelet állapotát. |
> | Műveletek | Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/olvasás | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Egy központi telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Áthelyezi az erőforrásokat az egyik erőforráscsoporthoz a másikba. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourcegroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Az erőforrások egyik erőforráscsoporthoz egy másikra történő áthelyezésének ellenőrzése. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/write | Létrehoz vagy frissít egy erőforráscsoportot. |
> | Műveletek | Microsoft. Resources/előfizetések/erőforrások/olvasás | Az előfizetés erőforrásainak beolvasása. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/delete | Törli az előfizetés címkéjét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/read | Lekérdezi vagy felsorolja az előfizetés címkéit. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Egy előfizetési címke értékének törlése. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Lekérdezi vagy felsorolja az előfizetési címke értékeit. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/tagValues/Write | Hozzáadja az előfizetési címke értékét. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/Write | Hozzáadja az előfizetés címkéjét. |
> | Műveletek | Microsoft.Resources/tags/delete | Eltávolítja az erőforrás összes címkéjét. |
> | Műveletek | Microsoft.Resources/tags/read | Lekéri az erőforrás összes címkéjét. |
> | Műveletek | Microsoft. Resources/Tags/Write | Frissíti egy erőforrás címkéit a meglévő címkék új címkékkel való lecserélésével vagy összevonásával, vagy a meglévő címkék eltávolításával. |
> | Műveletek | Microsoft.Resources/tenants/read | A bérlők listájának beolvasása. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Scheduler/jobcollections/delete | A feladatütemezés törlése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/disable/action | A feladatütemezés letiltása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/enable/action | A feladatütemezés engedélyezése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/delete | Törli a feladatot. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | A logikai alkalmazás definícióját ütemező feladatok alapján hozza létre. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/jobhistories/olvasás | Beolvassa a feladatok előzményeit. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/olvasás | Lekérdezi a feladatot. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/run/action | A feladatot futtatja. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/írás | Létrehozza vagy frissíti a feladatot. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/olvasás | Webhelycsoport beolvasása |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/írás | A feladatütemezés létrehozása vagy frissítése. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Search/checkNameAvailability/action | A szolgáltatás nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.Search/operations/read | A Microsoft. Search szolgáltató összes elérhető műveletét listázza. |
> | Műveletek | Microsoft.Search/register/action | Regisztrálja az előfizetést a keresési erőforrás-szolgáltatónál, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Műveletek | Microsoft.Search/searchServices/createQueryKey/action | Létrehozza a lekérdezési kulcsot. |
> | Műveletek | Microsoft.Search/searchServices/delete | A keresési szolgáltatás törlése. |
> | Műveletek | Microsoft.Search/searchServices/deleteQueryKey/delete | Törli a lekérdezési kulcsot. |
> | Műveletek | Microsoft.Search/searchServices/listAdminKeys/action | A rendszergazdai kulcsok beolvasása. |
> | Műveletek | Microsoft. Search/searchServices/listQueryKeys/művelet | Az adott Azure Cognitive Search szolgáltatáshoz tartozó lekérdezési API-kulcsok listáját adja vissza. |
> | Műveletek | Microsoft.Search/searchServices/listQueryKeys/read | Az adott Azure Cognitive Search szolgáltatáshoz tartozó lekérdezési API-kulcsok listáját adja vissza. |
> | Műveletek | Microsoft.Search/searchServices/read | A keresési szolgáltatás beolvasása. |
> | Műveletek | Microsoft.Search/searchServices/regenerateAdminKey/action | Újra létrehozza a felügyeleti kulcsot. |
> | Műveletek | Microsoft.Search/searchServices/start/action | Elindítja a keresési szolgáltatást. |
> | Műveletek | Microsoft.Search/searchServices/stop/action | Leállítja a keresési szolgáltatást. |
> | Műveletek | Microsoft. Search/searchServices/Write | A keresési szolgáltatás létrehozása vagy frissítése. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | A megadott forgalmi korlátozási szabályok betartatása a megadott hálózati biztonsági csoport (ok) hoz tartozó egyező biztonsági szabályok létrehozásával |
> | Műveletek | Microsoft.Security/adaptiveNetworkHardenings/read | Az Azure által védett erőforrások adaptív hálózattal kapcsolatos javaslatainak beolvasása |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/read | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak beolvasása |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/write | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak frissítése |
> | Műveletek | Microsoft.Security/alerts/read | Az összes elérhető biztonsági riasztás beolvasása |
> | Műveletek | Microsoft.Security/applicationWhitelistings/read | Az alkalmazás engedélyezési listájának beolvasása |
> | Műveletek | Microsoft.Security/applicationWhitelistings/write | Új alkalmazás-engedélyezési lista létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft. Security/assessmentMetadata/READ | Elérhető biztonsági értékelési metaadatok beolvasása az előfizetéshez |
> | Műveletek | Microsoft. Security/assessmentMetadata/írás | Biztonsági értékelés metaadatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. Security/értékelések/olvasás | Biztonsági értékelések beszerzése az előfizetésen |
> | Műveletek | Microsoft. Security/értékelések/írás | Biztonsági értékelések létrehozása vagy frissítése az előfizetésen |
> | Műveletek | Microsoft.Security/complianceResults/read | Az erőforrás megfelelőségi eredményeinek beolvasása |
> | Műveletek | Microsoft.Security/informationProtectionPolicies/read | Az erőforráshoz tartozó Information Protection-szabályzatok beolvasása |
> | Műveletek | Microsoft.Security/informationProtectionPolicies/write | Az erőforráshoz tartozó Information Protection-szabályzatok frissítése |
> | Műveletek | Microsoft.Security/locations/alerts/activate/action | Biztonsági riasztás aktiválása |
> | Műveletek | Microsoft.Security/locations/alerts/dismiss/action | Biztonsági riasztás bezárása |
> | Műveletek | Microsoft.Security/locations/alerts/read | Az összes elérhető biztonsági riasztás beolvasása |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Az igény szerinti hálózati hozzáférési házirend törlése |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Igény szerinti hálózati hozzáférési szabályzat kezdeményezése |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Az igény szerinti hálózati hozzáférési szabályzatok beolvasása |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Egy új, igény szerinti hálózati hozzáférési házirend létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/locations/read | A biztonsági adattárolási hely beolvasása |
> | Műveletek | Microsoft.Security/locations/tasks/activate/action | Biztonsági javaslat aktiválása |
> | Műveletek | Microsoft.Security/locations/tasks/dismiss/action | Biztonsági javaslat bezárása |
> | Műveletek | Microsoft.Security/locations/tasks/read | Az összes elérhető biztonsági javaslat beolvasása |
> | Műveletek | Microsoft.Security/locations/tasks/resolve/action | Biztonsági javaslat feloldása |
> | Műveletek | Microsoft.Security/locations/tasks/start/action | Biztonsági javaslat elindítása |
> | Műveletek | Microsoft.Security/policies/read | A biztonsági szabályzat beolvasása |
> | Műveletek | Microsoft.Security/policies/write | A biztonsági szabályzat frissítése |
> | Műveletek | Microsoft.Security/pricings/delete | A hatókör díjszabási beállításainak törlése |
> | Műveletek | Microsoft.Security/pricings/read | A hatókör díjszabási beállításainak beolvasása |
> | Műveletek | Microsoft.Security/pricings/write | A hatókör díjszabási beállításainak frissítése |
> | Műveletek | Microsoft.Security/register/action | Az előfizetés regisztrálása Azure Security Center |
> | Műveletek | Microsoft.Security/securityContacts/delete | A biztonsági kapcsolattartó törlése |
> | Műveletek | Microsoft.Security/securityContacts/read | A biztonsági kapcsolattartó beolvasása |
> | Műveletek | Microsoft.Security/securityContacts/write | A biztonsági kapcsolattartó frissítése |
> | Műveletek | Microsoft.Security/securitySolutions/delete | Biztonsági megoldás törlése |
> | Műveletek | Microsoft.Security/securitySolutions/read | A biztonsági megoldások beolvasása |
> | Műveletek | Microsoft.Security/securitySolutions/write | Új biztonsági megoldás létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/securitySolutionsReferenceData/read | A biztonsági megoldások hivatkozási adatkérésének beolvasása |
> | Műveletek | Microsoft. Security/securityStatuses/READ | Az Azure-erőforrások biztonsági állapotának beolvasása |
> | Műveletek | Microsoft.Security/securityStatusesSummaries/read | A hatókörre vonatkozó biztonsági állapotok összegzésének beolvasása |
> | Műveletek | Microsoft.Security/settings/read | A hatókör beállításainak beolvasása |
> | Műveletek | Microsoft.Security/settings/write | A hatókör beállításainak frissítése |
> | Műveletek | Microsoft. Security/feladatok/olvasás | Az összes elérhető biztonsági javaslat beolvasása |
> | Műveletek | Microsoft.Security/unregister/action | Az előfizetés regisztrációjának törlése Azure Security Center |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/read | A webalkalmazási tűzfalak beolvasása |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/write | Új webalkalmazási tűzfal létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/workspaceSettings/connect/action | Munkaterület-beállítások újrakapcsolási beállításainak módosítása |
> | Műveletek | Microsoft.Security/workspaceSettings/delete | A munkaterület beállításainak törlése |
> | Műveletek | Microsoft.Security/workspaceSettings/read | A munkaterület beállításainak beolvasása |
> | Műveletek | Microsoft.Security/workspaceSettings/write | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/write | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceBus/checkNameAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft.ServiceBus/checkNamespaceAvailability/action | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | A megadott VNet tartozó ServiceBus erőforrás-szolgáltató VNet-szabályainak törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/action | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/read | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/write | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft.ServiceBus/namespaces/Delete | Névtér erőforrásának törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Törli a névtérhez társított Event Grid szűrőt. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/read | A névtérhez társított Event Grid szűrő beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Létrehozza vagy frissíti a névtérhez társított Event Grid szűrőt. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP-szűrő erőforrásának törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP-szűrő erőforrásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP-szűrő erőforrásának létrehozása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Üzenetek küldése |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/read | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/write | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrate/action | Névtér átmigrálása művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Az áttelepítési konfiguráció törlése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Az áttelepítési konfiguráció beolvasása, amely jelzi az áttelepítés állapotát és a függőben lévő replikációs műveleteket. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | A standard és a prémium szintű névtér áttelepítésének visszaállítása |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Hozzárendeli a standard névtérhez tartozó DNS-t a prémium névtérhez, amely befejezi az áttelepítést, és leállítja az erőforrások szinkronizálását a standard és a prémium névtér között. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Az áttelepítési konfiguráció létrehozása vagy frissítése. Ezzel elindítja a standard és a prémium szintű névtér erőforrásainak szinkronizálását. |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/olvasás | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/write | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft.ServiceBus/namespaces/operationresults/read | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | A várólista frissítési művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Várólista-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | A várólistához tartozó kapcsolódási karakterlánc lekérése |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  A várólista-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Várólista-engedélyezési szabályok létrehozása és a tulajdonságainak frissítése. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/Delete | Várólista-erőforrás törlésének művelete |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/read | Várólista-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/write | Várólista tulajdonságainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceBus/namespaces/read | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | A témakör frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | A témakör engedélyezési szabályainak törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | A kapcsolódási karakterlánc lekérése a témakörhöz |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  A témakör engedélyezési szabályainak lekérése |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Hozzon létre a témakör engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/Delete | Témakör-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/read | Témakör-erőforrás leírások listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Szabály-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Szabály erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Szabály tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/write | Témakör tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | VNET-szabály erőforrásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft.ServiceBus/namespaces/write | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft.ServiceBus/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.ServiceBus/register/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóhoz, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Műveletek | Microsoft.ServiceBus/sku/read | SKU-erőforrások leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/sku/regions/read | SkuRegions-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.ServiceBus/unregister/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóhoz, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/delete | Bármely alkalmazás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/read | Bármely alkalmazás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/delete | Bármely szolgáltatás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Bármely partíció beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Bármely replika beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/read | Bármely szolgáltatás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Bármely szolgáltatás állapotának beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/write | Bármely szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/write | Bármely alkalmazás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Bármely alkalmazás típusának törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/read | Bármely alkalmazás típusának beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Bármely alkalmazás típusú verzió törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Bármely alkalmazás típusú verzió olvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Bármely alkalmazás típusú verzió létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/write | Bármely alkalmazás típusának létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/delete | Bármely fürt törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/nodes/read | Bármely csomópont beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/read | Bármely fürt beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/statuses/read | Bármely fürt állapotának olvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/write | Tetszőleges fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/locations/clusterVersions/read | Bármely fürt verziójának beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Adott környezethez tartozó bármely fürt verziójának beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/operationresults/read | A művelet eredményeinek olvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/operations/read | Bármilyen művelet beolvasása hely szerint |
> | Műveletek | Microsoft.ServiceFabric/operations/read | Az összes elérhető művelet beolvasása |
> | Műveletek | Microsoft.ServiceFabric/register/action | Bármilyen művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SignalRService/locations/checknameavailability/action | Annak ellenőrzése, hogy a név elérhető-e az új szignáló szolgáltatással való használatra |
> | Műveletek | Microsoft.SignalRService/locations/operationresults/signalr/read | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft.SignalRService/locations/usages/read | Az Azure Signaler szolgáltatás kvóta-használatának beolvasása |
> | Műveletek | Microsoft.SignalRService/operationresults/read | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft. SignalRService/Operations/READ | Az Azure Signaler szolgáltatás műveleteinek listázása. |
> | Műveletek | Microsoft.SignalRService/operationstatus/read | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft.SignalRService/register/action | Regisztrálja a Microsoft. SignalRService erőforrás-szolgáltatót egy előfizetéssel |
> | Műveletek | Microsoft.SignalRService/SignalR/delete | A teljes jelző szolgáltatás törlése |
> | Műveletek | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Event Grid-szűrő törlése a jelzőből. |
> | Műveletek | Microsoft.SignalRService/SignalR/eventGridFilters/read | A megadott Event Grid-szűrő tulajdonságainak beolvasása vagy a megadott jelzőhöz tartozó összes Event Grid-szűrő felsorolása. |
> | Műveletek | Microsoft.SignalRService/SignalR/eventGridFilters/write | A megadott paraméterekkel rendelkező jelzőhöz tartozó Event Grid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft.SignalRService/SignalR/listkeys/action | A Signaler-hozzáférési kulcsok értékének megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft.SignalRService/SignalR/read | A jelző beállításainak és konfigurációjának megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft.SignalRService/SignalR/regeneratekey/action | A Signaler-hozzáférési kulcsok értékének módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft.SignalRService/SignalR/restart/action | Az Azure Signaler szolgáltatás újraindítása a felügyeleti portálon vagy API-n keresztül. Bizonyos állásidő lesz. |
> | Műveletek | Microsoft.SignalRService/SignalR/write | A jelző beállításainak és konfigurációjának módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft.SignalRService/unregister/action | A Microsoft. SignalRService erőforrás-szolgáltató regisztrációjának törlése előfizetéssel |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Felsorolja az alkalmazás definíciójának alkalmazási összetevőit. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/delete | Eltávolít egy alkalmazás-definíciót. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/read | Az alkalmazás-definíciók listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/write | Alkalmazás-definíció hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.Solutions/applications/applicationArtifacts/read | Felsorolja az alkalmazás-összetevőket. |
> | Műveletek | Microsoft.Solutions/applications/delete | Eltávolít egy alkalmazást. |
> | Műveletek | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applications/refreshPermissions/action | Frissíti az alkalmazás engedélyeit. |
> | Műveletek | Microsoft.Solutions/applications/updateAccess/action | Frissíti az alkalmazás-hozzáférést. |
> | Műveletek | Microsoft.Solutions/applications/write | Létrehoz egy alkalmazást. |
> | Műveletek | Microsoft.Solutions/jitRequests/delete | JitRequest eltávolítása |
> | Műveletek | Microsoft.Solutions/jitRequests/read | JitRequests listájának beolvasása |
> | Műveletek | Microsoft.Solutions/jitRequests/write | Létrehoz egy JitRequest |
> | Műveletek | Microsoft.Solutions/locations/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.Solutions/register/action | Regisztráljon a megoldásokra. |
> | Műveletek | Microsoft.Solutions/unregister/action | A megoldások regisztrációjának törlése. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Sql/checkNameAvailability/action | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft.Sql/instancePools/delete | Egy példány készletének törlése |
> | Műveletek | Microsoft.Sql/instancePools/read | Egy példány készletének beolvasása |
> | Műveletek | Microsoft.Sql/instancePools/usages/read | Példány-készlet használati adatainak beolvasása |
> | Műveletek | Microsoft.Sql/instancePools/write | Egy példány-készlet létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/capabilities/read | Az előfizetéshez tartozó képességek beolvasása egy adott helyen |
> | Műveletek | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/databaseOperationResults/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Műveletek | Microsoft.Sql/locations/deletedServerOperationResults/read | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Műveletek | Microsoft.Sql/locations/deletedServers/read | Visszaadja a törölt kiszolgálók listáját, vagy lekéri a megadott törölt kiszolgáló tulajdonságait. |
> | Műveletek | Microsoft.Sql/locations/deletedServers/recover/action | Törölt kiszolgáló helyreállítása |
> | Műveletek | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Egy rugalmas készlet aszinkron műveletéhez tartozó Azure aszinkron művelet beolvasása |
> | Műveletek | Microsoft.Sql/locations/elasticPoolOperationResults/read | Egy rugalmas készlet műveletének eredményét kapja meg. |
> | Műveletek | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Műveletek | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/firewallRulesOperationResults/read | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/delete | Töröl egy meglévő példány-feladatátvételi csoportot. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Tervezett feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/read | A példányok feladatátvételi csoportjainak listáját adja vissza, vagy lekéri a megadott példány-feladatátvételi csoport tulajdonságait. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/write | Létrehoz egy példány-feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott példány-feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Egy példány-készlet művelet állapotának beolvasása |
> | Műveletek | Microsoft.Sql/locations/instancePoolOperationResults/read | Egy példány-készlet művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Egy adott interfész-végpont Azure aszinkron műveletének részleteit adja vissza. |
> | Műveletek | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | A megadott csatoló-végponti profil műveletének részleteit adja vissza. |
> | Műveletek | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Lekéri egy ügynök műveletének állapotát. |
> | Műveletek | Microsoft.Sql/locations/jobAgentOperationResults/read | Lekérdezi egy ügynök műveletének eredményét. |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionBackups/read | Felsorolja a hely összes kiszolgálójának összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Felsorolja a kiszolgáló összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Hosszú távú adatmegőrzési biztonsági mentés törlése |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Egy adatbázis hosszú távú adatmegőrzési biztonsági másolatait sorolja fel |
> | Műveletek | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Felügyelt adatbázis-visszaállítási művelet befejezése |
> | Műveletek | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Műveletek | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Műveletek | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Műveletek | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/read | Egy adott előfizetés elérhető helyeinek beolvasása |
> | Műveletek | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft.Sql/locations/serverKeyOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft.Sql/locations/syncAgentOperationResults/read | A Szinkronizáló ügynök erőforrás-műveletének eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncDatabaseIds/read | Egy adott régió és előfizetés szinkronizálási adatbázis-azonosítóinak beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncGroupOperationResults/read | A szinkronizálási csoport erőforrása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncMemberOperationResults/read | A szinkronizálási tag erőforrása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/usages/read | Az előfizetéshez tartozó használati metrikák gyűjteményének beolvasása egy helyen |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | A megadott virtuális hálózati szabályok Azure aszinkron műveletének részleteit adja vissza.  |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | A virtuális hálózati szabályok megadott műveletének részleteit adja vissza.  |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/delete | A felügyelt példány meglévő rendszergazdája törölve. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/read | Lekéri a felügyelt példányok rendszergazdáinak listáját. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/write | A felügyelt példányok rendszergazdájának létrehozása vagy frissítése a megadott paraméterekkel. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/oszlopok/olvasás | Egy felügyelt adatbázis oszlopainak listájának visszaadása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | A Batch Update érzékenységi címkéi |
> | Műveletek | Microsoft.Sql/managedInstances/databases/delete | Meglévő felügyelt adatbázis törlése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Lekéri a felügyelt példány adatbázisaihoz elérhető naplókat |
> | Műveletek | Microsoft.Sql/managedInstances/databases/read | Meglévő felügyelt adatbázis beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/írás | A Batch Update ajánlott érzékenységi címkéi |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/restoreDetails/READ | A felügyelt adatbázis-visszaállítás részleteit adja vissza, amíg a visszaállítás folyamatban van. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/read | Felügyelt adatbázis-séma beszerzése. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Felügyelt adatbázis oszlopának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Felügyelt adatbázistábla beszerzése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Az adott kiszolgálóhoz konfigurált felügyelt adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott felügyelt adatbázis esetében |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityEvents/read | A felügyelt adatbázis biztonsági eseményeinek beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Az adatbázis transzparens adattitkosítás adatainak beolvasása egy adott felügyelt adatbázisban |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Az adatbázis transzparens adattitkosítás módosítása egy adott felügyelt adatbázisra vonatkozóan |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/write | Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist. |
> | Műveletek | Microsoft.Sql/managedInstances/delete | Egy meglévő felügyelt példány törlése. |
> | Műveletek | Microsoft.Sql/managedInstances/encryptionProtector/read | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft.Sql/managedInstances/encryptionProtector/write | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/delete | Egy meglévő Azure SQL felügyelt példány kulcsának törlése. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/read | Visszaadja a felügyelt példány kulcsainak listáját, vagy lekéri a megadott felügyelt példány kulcsának tulajdonságait. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány kulcsának tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/managedInstances/metricDefinitions/read | Felügyelt példány metrika-definícióinak beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/metrics/read | Felügyelt példány metrikáinak beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | A felügyelt példányok elérhető naplófájljainak beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | A felügyelt példányok számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/managedInstances/read | Visszaadja a felügyelt példányok listáját, vagy lekéri a megadott felügyelt példány tulajdonságait. |
> | Műveletek | Microsoft.Sql/managedInstances/recoverableDatabases/read | A helyreállítható felügyelt adatbázisok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Rövid távú adatmegőrzési szabályzat beolvasása egy eldobott felügyelt adatbázishoz |
> | Műveletek | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Egy eldobott felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Visszaállítható felügyelt adatbázisok listájának beolvasása. |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Egy adott kiszolgálóhoz konfigurált felügyelt kiszolgáló veszélyforrás-észlelési szabályzatok listájának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Felügyelt kiszolgáló fenyegetés-észlelési szabályzatának módosítása egy adott felügyelt kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE-tanúsítvány létrehozása/frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Egy adott felügyelt példány sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy adott felügyelt példányon |
> | Műveletek | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Egy adott felügyelt példány sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/write | Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/operations/read | Elérhető REST-műveletek beolvasása |
> | Műveletek | Microsoft. SQL/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Műveletek | Microsoft.Sql/register/action | Regisztrálja a Microsoft SQL Database erőforrás-szolgáltató előfizetését, és engedélyezi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft.Sql/servers/administrators/delete | Egy adott Azure Active Directory rendszergazdai objektum törlése |
> | Műveletek | Microsoft.Sql/servers/administrators/read | Egy adott Azure Active Directory rendszergazdai objektum beolvasása |
> | Műveletek | Microsoft.Sql/servers/administrators/write | Egy adott Azure Active Directory rendszergazdai objektum hozzáadására vagy frissítésére szolgál. |
> | Műveletek | Microsoft.Sql/servers/advisors/read | A kiszolgálóhoz elérhető tanácsadók listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/read | A kiszolgáló megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/write | A javasolt művelet alkalmazása a kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/advisors/write | A tanácsadók kiszolgálói szinten történő automatikus végrehajtási állapotának frissítése. |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/read | Az adott kiszolgálón konfigurált alapértelmezett kiszolgálóoldali naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/write | Az alapértelmezett kiszolgálói tábla naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/operationResults/read | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/read | Egy adott kiszolgálón konfigurált kiszolgálói blob naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/write | A kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/read | A kiszolgáló Automatikus hangolási beállításainak visszaadása |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/write | Frissíti a kiszolgáló Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Töröl egy meglévő biztonsági mentési archiváló tárat. |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ezzel a művelettel egy biztonsági mentési hosszú távú adatmegőrzési tár olvasható be. Információt ad vissza a kiszolgálón regisztrált tárolóról |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ezzel a művelettel lehet regisztrálni egy biztonsági mentési hosszú távú adatmegőrzési tárolót egy kiszolgálóra |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/delete | Egy meglévő kiszolgálói kommunikációs hivatkozás törlése. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/read | Egy megadott kiszolgáló kommunikációs hivatkozásainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/write | Kiszolgáló-kommunikációs hivatkozás létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/read | Egy adott kiszolgáló kiszolgálói csatlakoztatási házirendjeinek a listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/write | Kiszolgálói kapcsolatok házirendjének létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/read | Az adatbázishoz elérhető tanácsadók listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Az adatbázis megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | A javasolt művelet alkalmazása az adatbázison |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/write | Egy Advisor automatikus végrehajtási állapotának frissítése az adatbázis szintjén. |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/read | Az adott adatbázisban konfigurált táblázatos naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/write | A tábla naplózási házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/read | Egy adott adatbázisban konfigurált blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/write | Egy adott adatbázis blob-naplózási házirendjének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/read | Egy adatbázis Automatikus hangolási beállításainak visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/write | Frissíti az adatbázis Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Műveletek | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Egy adott adatbázis biztonsági mentési archiválási házirendjeinek a listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Adatbázis biztonsági mentési archiválási szabályzatának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Egy adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Egy adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/oszlopok/olvasás | Egy adatbázis oszlopainak listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/read | Egy adott adatbázisban konfigurált kapcsolati szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/write | Egy adott adatbázishoz tartozó kapcsolatbiztonsági szabályzat módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | A Batch Update érzékenységi címkéi |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Az adatbázis-adatmaszkolási szabályzatok listájának visszaadása. |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Az adatmaszkolási házirend szabályának törlése egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Az adott adatbázisban konfigurált adatmaszkolási házirend-szabály részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Az adatmaszkolási házirend szabályának módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Egy adott adatbázis adatmaszkolási házirendjének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Az adatraktár-lekérdezés elosztott lekérdezési lépésének információit adja vissza a kiválasztott lépés-AZONOSÍTÓhoz |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Az adatraktár-terjesztési lekérdezési adatokat adja vissza a kiválasztott lekérdezési AZONOSÍTÓhoz. |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Lekéri egy SQL Data Warehouse példány felhasználói tevékenységét, amely tartalmazza a futó és a felfüggesztett lekérdezéseket |
> | Műveletek | Microsoft.Sql/servers/databases/delete | Töröl egy meglévő adatbázist. |
> | Műveletek | Microsoft.Sql/servers/databases/export/action | Azure SQL Database exportálása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Egy adott adatbázis kiterjesztett blob-naplózási szabályzatának módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/read | Az adatbázishoz tartozó bővítmények gyűjteményét kéri le. |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/write | Egy adott adatbázis kiterjesztésének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/failover/action | Ügyfél által kezdeményezett adatbázis-feladatátvétel. |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Egy adott adatbázishoz tartozó földrajzi biztonsági mentési szabályzatok beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Adatbázis-geobackup szabályzat létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/importExportOperationResults/read | Folyamatban lévő importálási/exportálási műveletek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | A kiválasztott adatbázishoz elérhető karbantartási időszakok listájának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindows/read | A kiválasztott adatbázis karbantartási Windows-beállításainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindows/write | Beállítja a karbantartási Windows-beállításokat a kiválasztott adatbázishoz. |
> | Műveletek | Microsoft.Sql/servers/databases/metricDefinitions/read | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/servers/databases/metrics/read | Adatbázisok visszaküldési metrikái |
> | Műveletek | Microsoft.Sql/servers/databases/move/action | Meglévő adatbázis nevének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/operationResults/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/cancel/action | Megszakítja Azure SQL Database folyamatban lévő aszinkron műveletet, amely még nem fejeződött be. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/read | Az adatbázison végrehajtott műveletek listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/pause/action | Az Azure SQL Datawarehouse-adatbázis szüneteltetése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | A rendelkezésre álló naplók beolvasása az adatbázisokhoz |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | A megadott paramétereknek megfelelő lekérdezési szövegek gyűjteményét adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/read | Az adatbázishoz tartozó Query Store-beállítások aktuális értékeit adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/write | Frissíti az adatbázis lekérdezés-tárolójának beállításait |
> | Műveletek | Microsoft.Sql/servers/databases/read | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/írás | A Batch Update ajánlott érzékenységi címkéi |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/delete | A replikálási kapcsolat kényszerített leállítása és lehetséges adatvesztése |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Feladatátvétel az elsődlegesről az összes módosítás szinkronizálása után, hogy az adatbázis az elsődleges replikációs relationship\u0027s legyen, és hogy a távoli elsődleges legyen a másodlagos |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Azonnali feladatátvétel a lehetséges adatvesztéssel, az adatbázis replikálása az elsődleges replikálási relationship\u0027s, és a távoli elsődleges folyamat másodlagosra állítása |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/read | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | A replikációs kapcsolat kényszerített leállítása vagy a partnerrel való szinkronizálás után |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Replikációs mód frissítése a szinkron vagy aszinkron üzemmódra való hivatkozáshoz |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/action | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/delete | Törli az adatbázis visszaállítási pontját. |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/read | Az adatbázis visszaállítási pontjait adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/resume/action | Az Azure SQL Datawarehouse-adatbázis folytatása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/read | Adatbázis-séma beszerzése. |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Adatbázis oszlopának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/read | Adatbázis táblázatának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Tárgymutató-javaslatok listájának beolvasása egy adatbázison |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Tárgymutató-javaslat alkalmazása |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Az adott kiszolgálóhoz konfigurált adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft.Sql/servers/databases/securityMetrics/read | Adatbázis-biztonsági mérőszámok gyűjteményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | A teljesítmény növelése vagy a költséghatékonyság csökkentése érdekében a lekérdezés végrehajtási statisztikái alapján fel-vagy leskálázásával kapcsolatos javaslat visszaküldése |
> | Műveletek | Microsoft.Sql/servers/databases/skus/read | Egy adatbázishoz elérhető SKU-gyűjtemény beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Szinkronizálási csoport szinkronizálásának megszakítása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/delete | Töröl egy meglévő szinkronizálási csoportot. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | A Szinkronizáló központ adatbázis-sémáinak listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/logs/read | A szinkronizálási csoport naplóiból álló lista visszaküldése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/read | Visszaadja a szinkronizálási csoportok listáját, vagy lekéri a megadott szinkronizálási csoport tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | A Szinkronizáló központ adatbázis-sémájának frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | A szinkronizálási központ sémájának frissítési művelete eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Töröl egy meglévő szinkronizálási tagot. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Visszaadja a szinkronizálási tagok listáját, vagy lekéri a megadott szinkronizálási tag tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Szinkronizálási tag sémájának frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | A szinkronizálási tag séma-frissítési műveletének eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | A szinkronizálási tag adatbázis-sémáinak listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Egy szinkronizálási tagot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási tag tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Szinkronizálási csoport szinkronizálásának elindítása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/write | Egy szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási csoport tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/queryText/action | A kiválasztott lekérdezési azonosító Transact-SQL-szövegét adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/read | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/statistics/read | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítással |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Egy adott adatbázis transzparens adattitkosítási biztonsági funkciójának állapotának és részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Transzparens adattitkosítási állapot módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Az Azure SQL Datawarehouse-adatbázis frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/usages/read | A Azure SQL Database használati adatok beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Az adatbázis sebezhetőség-felmérési vizsgálatának végrehajtási műveletének eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Egy adott adatbázison konfigurált sebezhetőségi felmérés részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/write | Létrehoz egy adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Egy meglévő vész-helyreállítási konfiguráció törlése egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration feladatátvétele |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration-feladatátvétel kényszerítése |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | A kiszolgálót tartalmazó vész-helyreállítási konfigurációk gyűjteményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | A kiszolgáló vész-helyreállítási konfigurációjának módosítása |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/read | A kiszolgálóhoz már létrehozott rugalmas készletre vonatkozó becslések listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/write | Új rugalmas készletre vonatkozó becslést hoz létre a megadott adatbázisok listájához |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/read | A rugalmas készlethez elérhető tanácsadók listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | A rugalmas készlethez megadott tanácsadó által javasolt műveletek listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | A javasolt művelet alkalmazása a rugalmas készleten |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/write | Az Advisor automatikus végrehajtási állapotának frissítése rugalmas készlet szintjén. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/databases/read | A rugalmas készlethez tartozó adatbázisok listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/delete | Meglévő rugalmas készlet törlése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Tevékenységek és részletek beolvasása egy adott rugalmas adatbázis-készleten |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Tevékenységek és részletek beolvasása egy adott adatbázisra, amely a rugalmas adatbázis-készlet részét képezi. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/failover/action | Az ügyfél által kezdeményezett rugalmas készlet feladatátvétele. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metrics/read | Rugalmas adatbázis-készletek visszaküldési metrikái |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Megszakítja az Azure SQL rugalmas készlet megszakítását, amely még nem fejeződött be aszinkron művelet. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/read | A rugalmas készleten végrehajtott műveletek listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/servers/elasticPools/read | Az adott kiszolgálón található rugalmas készlet adatainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/skus/read | Egy rugalmas készlethez rendelkezésre álló SKU-gyűjtemény beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/write | Meglévő rugalmas készlet új vagy módosított tulajdonságainak létrehozása |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/read | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Műveletek | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/write | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/read | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/write | A kiterjesztett kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/delete | Töröl egy meglévő feladatátvételi csoportot. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/failover/action | Tervezett feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/read | A feladatátvételi csoportok listáját adja vissza, vagy lekéri a megadott feladatátvételi csoport tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/write | Létrehoz egy feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/delete | Töröl egy meglévő kiszolgálói tűzfalszabály-szabályt. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/read | Visszaadja a kiszolgálói tűzfalszabályok listáját, vagy lekéri a megadott kiszolgálói tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/write | Létrehoz egy kiszolgálói tűzfalszabály a megadott paraméterekkel, frissíti a megadott szabály tulajdonságait, vagy felülírja az összes meglévő szabályt az új kiszolgálói tűzfalszabályok (ok) használatával. |
> | Műveletek | Microsoft.Sql/servers/import/action | Új adatbázis létrehozása a kiszolgálón és a séma és az adatok központi telepítése DacPac-csomagból |
> | Műveletek | Microsoft.Sql/servers/importExportOperationResults/read | Folyamatban lévő importálási/exportálási műveletek beolvasása |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | A megadott csatoló-végponti profil törlése |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/read | A megadott csatoló-végpont profiljának tulajdonságait adja vissza. |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Létrehoz egy illesztőfelület-végponti profilt a megadott paraméterekkel, vagy frissíti a megadott csatoló-végpont tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/servers/jobAgents/delete | Az Azure SQL DB-feladatok ügynökének törlése |
> | Műveletek | Microsoft.Sql/servers/jobAgents/read | Azure SQL Database-feladatok ügynökének beolvasása |
> | Műveletek | Microsoft.Sql/servers/jobAgents/write | Létrehoz vagy frissít egy Azure SQL DB-feladatot kezelő ügynököt |
> | Műveletek | Microsoft.Sql/servers/keys/delete | Töröl egy meglévő kiszolgálói kulcsot. |
> | Műveletek | Microsoft.Sql/servers/keys/read | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/servers/operationResults/read | Folyamatban lévő kiszolgálói műveletek beolvasása |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/read | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Műveletek | Microsoft.Sql/servers/privateLinkResources/read | A kapcsolódó SQL Serverhez tartozó magánhálózati kapcsolati erőforrások beolvasása |
> | Műveletek | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | A kiszolgálók számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.Sql/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Az ajánlott rugalmas adatbázis-készletek metrikáinak beolvasása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/read | A rugalmas adatbázis-készletek javaslatának beolvasása, amely csökkenti a költségeket, vagy javítja a teljesítményt a korábbi erőforrások kihasználtsága alapján |
> | Műveletek | Microsoft.Sql/servers/recoverableDatabases/read | Ez a művelet az élő adatbázis vész-helyreállítására szolgál az adatbázis utolsó ismert jó biztonsági mentési pontra való visszaállításához. Az utolsó jó biztonsági mentésre vonatkozó adatokat ad vissza, de az doesn\u0027t valójában visszaállítja az adatbázist. |
> | Műveletek | Microsoft.Sql/servers/replicationLinks/read | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/restorableDroppedDatabases/read | Azon adatbázisok listájának lekérése, amelyek a megőrzési házirendben még nem találhatók meg az adott kiszolgálón. |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | A kiszolgálói veszélyforrások észlelése házirend írási műveletének eredményeinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/read | Egy adott kiszolgálóhoz konfigurált kiszolgálói veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/serviceObjectives/read | Az adott kiszolgálón elérhető szolgáltatási szintű célkitűzések (más néven teljesítményszint) listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/delete | Töröl egy meglévő szinkronizálási ügynököt. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/generateKey/action | Szinkronizálási ügynök regisztrációs kulcsának előállítása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | A szinkronizálási ügynökhöz csatolt adatbázisok listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/read | Visszaadja a Szinkronizáló ügynökök listáját, vagy lekéri a megadott szinkronizálási ügynök tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/write | Létrehoz egy szinkronizáló ügynököt a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási ügynök tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/tdeCertificates/action | TDE-tanúsítvány létrehozása/frissítése |
> | Műveletek | Microsoft.Sql/servers/usages/read | A kiszolgáló DTU-kvótájának és aktuális DTU-felhasználásának visszaadása a kiszolgálón található összes adatbázis esetében |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Egy adott kiszolgáló sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft.Sql/servers/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy adott kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/vulnerabilityAssessments/write | Egy adott kiszolgáló sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft.Sql/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft.Sql/unregister/action | A Microsoft SQL Database erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése és a Microsoft SQL-adatbázisok létrehozásának engedélyezése. |
> | Műveletek | Microsoft.Sql/virtualClusters/delete | Töröl egy meglévő virtuális fürtöt. |
> | Műveletek | Microsoft.Sql/virtualClusters/read | Visszaadja a virtuális fürtök listáját, vagy lekéri a megadott virtuális fürt tulajdonságait. |
> | Műveletek | Microsoft.Sql/virtualClusters/write | Frissíti a virtuális fürtök címkéit. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Storage/checknameavailability/read | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft.Storage/locations/checknameavailability/read | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft. Storage értesítése arról, hogy a virtuális hálózat vagy az alhálózat törölve lett |
> | Műveletek | Microsoft.Storage/locations/usages/read | A megadott előfizetésben lévő erőforrások korlátját és aktuális használati számát adja vissza. |
> | Műveletek | Microsoft.Storage/operations/read | Egy aszinkron művelet állapotát kérdezi le. |
> | Műveletek | Microsoft.Storage/register/action | Regisztrálja az előfizetést a tárolási erőforrás-szolgáltató számára, és lehetővé teszi a Storage-fiókok létrehozását. |
> | Műveletek | Microsoft.Storage/skus/read | Felsorolja a Microsoft. Storage által támogatott SKU-ket. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | A blob-tartalom hozzáadásának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Egy automatikus pillanatkép törlésének eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Egy olyan fiókban lévő Blobok listáját adja vissza, amelyeknek megfelelő címkék szűrője van |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy Blobok listáját adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | A blob parancs eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | A blob-címkék olvasásának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | A blob-címkék írásának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | BLOB Container jogi megtartásának törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | BLOB-tároló módosíthatatlansági-házirendjének törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | BLOB-tároló módosíthatatlansági-házirendjének kiterjesztése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | BLOB-tároló módosíthatatlansági szabályzatának zárolása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | BLOB-tároló módosíthatatlansági szabályzatának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | BLOB-tároló módosíthatatlansági szabályzatának elhelyezése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | A blob-tároló bérletének eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót ad vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tárolók listájának visszaadása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | BLOB-tároló jogi megtartásának beállítása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/write | A blob-tároló javításának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/write | A blob-tároló Put eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Egy felhasználói delegálási kulcsot ad vissza a blob szolgáltatáshoz |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/read | A blob szolgáltatás tulajdonságainak vagy statisztikáinak visszaadása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/write | A Put blob szolgáltatás tulajdonságainak eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/delete | Töröl egy meglévő Storage-fiókot. |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/írás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/feladatátvétel/művelet | Az ügyfél a rendelkezésre állási problémák esetén képes a feladatátvétel vezérlésére |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Fájl-rendszergazdai jogosultságok beolvasása |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Egy fájlra vagy mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát, vagy a fájlok/mappák listáját adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/read | File Service-tulajdonságok beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/delete |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/READ |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/READ |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/Write |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/írás |  |
> | Műveletek | Microsoft.Storage/storageAccounts/listAccountSas/action | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/listkeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/listServiceSas/action | A megadott Storage-fiókhoz tartozó szolgáltatás SAS-jogkivonatát adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/managementPolicies/delete | Storage-fiókok felügyeleti házirendjeinek törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/managementPolicies/read | Storage-felügyeleti fiókok házirendjeinek beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/managementPolicies/write | Storage-fiókok felügyeleti házirendjeinek elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/írás |  |
> | Műveletek | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Privát végponti kapcsolatok Proxyinak törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/olvasás | Privát végponti kapcsolatok proxyjának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Magánhálózati végponti kapcsolatok Proxyinak elhelyezése |
> | Műveletek | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Privát végponti kapcsolatok beszerzése |
> | Műveletek | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Magánhálózati végponti kapcsolatok elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/művelet | Privát végponti kapcsolatok jóváhagyása |
> | Műveletek | Microsoft.Storage/storageAccounts/privateLinkResources/read | StorageAccount-groupids beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Egy várólista törlésének eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadásának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Egy üzenet törlésének eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Egy üzenet feldolgozásának eredményét adja vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Egy üzenet írásának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólistát vagy várólisták listáját adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/write | Egy üzenetsor írásának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/read | Queue szolgáltatás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/read | A várólista-szolgáltatás tulajdonságait vagy statisztikáit adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/write | A várólista-szolgáltatás tulajdonságainak beállításának eredményét adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Műveletek | Microsoft.Storage/storageAccounts/regeneratekey/action | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | Műveletek | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | BLOB-tartományok visszaállítása a megadott idő állapotára |
> | Műveletek | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | A megadott Storage-fiók összes felhasználói delegálási kulcsának visszavonása. |
> | Műveletek | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Storage-fiók diagnosztikai beállításainak létrehozása/frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/read | Table service tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/write | Létrehoz egy Storage-fiókot a megadott paraméterekkel, vagy frissíti a tulajdonságokat vagy címkéket, vagy hozzáadja az egyéni tartományt a megadott Storage-fiókhoz. |
> | Műveletek | Microsoft.Storage/usages/read | A megadott előfizetésben lévő erőforrások korlátját és aktuális használati számát adja vissza. |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.storagesync/locations/checkNameAvailability/action | Ellenőrzi, hogy a Storage Sync szolgáltatás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. storagesync/helyszínek/munkafolyamatok/műveletek/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/Operations/READ | A támogatott műveletek listájának beolvasása |
> | Műveletek | Microsoft. storagesync/regisztráció/művelet | Regisztrálja az előfizetést a Storage Sync Provider számára |
> | Műveletek | microsoft.storagesync/storageSyncServices/delete | A Storage Sync szolgáltatásainak törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Storage Sync Services elérhető metrikáinak beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/read | A Storage Sync szolgáltatásainak beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | A regisztrált kiszolgáló elérhető metrikáinak beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/read | Bármely regisztrált kiszolgáló beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/write | Bármely regisztrált kiszolgáló létrehozása vagy frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Bármilyen Felhőbeli végpont törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Aszinkron biztonsági mentési/visszaállítási művelet állapotának beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | A művelet meghívása a biztonsági mentés után |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | A művelet meghívása a visszaállítás után |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | A művelet meghívása a biztonsági mentés előtt |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | A művelet meghívása a visszaállítás előtt |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Felhőbeli végpontok beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Szívverés visszaállítása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Felhőalapú végpontok létrehozása vagy frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/delete | Szinkronizált csoportok törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A szinkronizálási csoportok elérhető metrikáinak beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/read | Bármely szinkronizálási csoport olvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Bármely kiszolgálói végpont törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. bepillantást/metricDefinitions/READ | A kiszolgálói végpontok elérhető metrikáinak beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Bármely kiszolgálói végpont olvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | A művelet meghívása a fájlok egy kiszolgálóra való felidézéséhez |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Bármely kiszolgálói végpont létrehozása vagy frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/write | Szinkronizált csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/operationresults/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/műveletek/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/olvasás | Munkafolyamatok olvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/Write | Tárolási szinkronizálási szolgáltatások létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/regisztráció/művelet | A Storage Sync Provider előfizetés regisztrációjának törlése |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/delete | Törli a Access Control rekordokat. |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/read | A Access Control rekordok listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/write | A Access Control rekordok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/alerts/read | A riasztások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/backups/read | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/delete | Meglévő sávszélesség-beállítások törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/read | A sávszélesség-beállítások listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/write | Új vagy frissített sávszélesség-beállításokat hoz létre (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/certificates/write | Tanúsítványok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/Managers/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Műveletek | Microsoft.StorSimple/managers/clearAlerts/action | Törölje az Eszközkezelőhöz társított összes riasztást. |
> | Műveletek | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | A Cloud Appliance által támogatott konfigurációk listázása |
> | Műveletek | Microsoft.StorSimple/managers/configureDevice/action | Eszköz konfigurálása |
> | Műveletek | Microsoft.StorSimple/managers/delete | Az eszközkezelők törlése |
> | Műveletek | Microsoft.StorSimple/Managers/delete | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/read | A riasztási beállítások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/write | Riasztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | A szolgáltatás titkosítási kulcsának átváltásának engedélyezése az eszközökön |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Készítsen manuális biztonsági mentést a szabályzat által védett összes kötetre vonatkozó igény szerinti biztonsági mentés létrehozásához. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Meglévő biztonsági mentési házirendek törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/read | A biztonsági mentési házirendek listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Meglévő ütemtervek törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Az ütemtervek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Új vagy frissítési ütemtervet hoz létre |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/write | Új vagy frissítési biztonsági mentési szabályzatok létrehozása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/delete | A biztonságimásolat-készlet törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Megosztás vagy kötet klónozása egy biztonsági mentési elem használatával. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/olvasás | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/restore/action | Állítsa vissza az összes kötetet egy biztonságimásolat-készletből. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | A biztonsági mentési ütemterv csoportok törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/olvasás | A biztonsági mentési ütemterv csoportok listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | A biztonsági mentési ütemterv csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/delete | A CHAP-beállítások törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/read | A CHAP-beállítások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/write | A CHAP-beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/inaktiválás/művelet | Inaktiválja az eszközt. |
> | Műveletek | Microsoft.StorSimple/managers/devices/delete | Az eszközök törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/disks/read | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/Letöltés/művelet | Egy eszköz frissítéseinek letöltése. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/művelet | Az eszköz feladatátvétele. |
> | Műveletek | Microsoft.StorSimple/managers/devices/failover/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/failoverTargets/olvasás | Felsorolja vagy lekéri az eszközök feladatátvételi céljait |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Készítsen biztonsági másolatot a fájlkiszolgálón. |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/delete | A fájlkiszolgálók törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/read | A fájlkiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | A megosztások törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/read | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Megosztások létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/write | Fájlkiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Hardver-összetevő csoportok vezérlő energiagazdálkodási állapotának módosítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/hardwareComponentGroups/olvasás | A hardver-összetevő csoportjai listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/telepítés/művelet | Frissítések telepítése az eszközön. |
> | Műveletek | Microsoft.StorSimple/managers/devices/installUpdates/action | Telepíti a frissítéseket az eszközökön (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Készítsen biztonsági másolatot egy iSCSI-kiszolgálóról. |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Az iSCSI-kiszolgálók törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | A lemezek törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Lemezek létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/read | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/write | ISCSI-kiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/megszakítás/művelet | Futó feladat megszakítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/olvasás | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Egy meglévő eszközhöz tartozó feladatátvevő készletek listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/listFailoverTargets/művelet | Az eszközök feladatátvételi céljainak listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft.StorSimple/managers/devices/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigration/Action | Ellenőrzi a sikeres áttelepítést, és véglegesíti azt. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigrationStatus/READ | Az áttelepítés megerősítési állapotának listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Az áttelepítés megerősítési állapotának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Az áttelepítési becslési feladatokhoz tartozó állapot beolvasása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationStatus/Action | Az áttelepítés állapotának beolvasása. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/migrationSourceConfigurations/importálás/művelet | Forrás-konfigurációk importálása az áttelepítéshez |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationEstimate/READ | Az áttelepítési becslés listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Az áttelepítés állapotának listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigration/Action | Áttelepítés indítása a forrás-konfigurációk használatával |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigrationEstimate/Action | Az áttelepítési folyamat időtartamának becsléséhez indítsa el a feladatot. |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/read | A hálózati beállítások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/write | Új vagy frissített hálózati beállításokat hoz létre |
> | Műveletek | Microsoft.StorSimple/managers/devices/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Az Eszközkezelő nyilvános titkosítási kulcsának listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Egy meglévő eszköz támogatási csomagjának közzététele. A StorSimple-támogatási csomag egy könnyen használható mechanizmus, amely az összes releváns naplót gyűjti, hogy segítséget nyújtson Microsoft ügyfélszolgálatanek a StorSimple-eszközök hibáinak elhárításában. |
> | Műveletek | Microsoft.StorSimple/managers/devices/read | Az eszközök listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Frissítések keresése az eszközön. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/read | A biztonsági beállítások listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Egy eszköz távfelügyeleti tanúsítványának szinkronizálása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Frissítse a biztonsági beállításokat. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/write | Új vagy frissített biztonsági beállítások létrehozása |
> | Műveletek | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Tesztüzenet küldése e-mailben a konfigurált e-mail-címzetteknek. |
> | Műveletek | Microsoft.StorSimple/managers/devices/shares/read | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/read | Felsorolja vagy lekéri az időbeállításokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/write | Új vagy frissítési idő beállításainak létrehozása |
> | Műveletek | Microsoft.StorSimple/managers/devices/updates/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/updateSummary/read | Felsorolja vagy lekéri a frissítés összegzését |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Meglévő mennyiségi tárolók törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | A metrikák listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | A metrikák definícióinak listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/read | A mennyiségi tárolók listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Meglévő kötetek törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | A metrikák listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | A metrikák definícióinak listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | A művelet eredményeinek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Kötetek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Új vagy frissített kötetek létrehozása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/write | Létrehoz egy új vagy frissített kötet-tárolót (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumes/read | Kötetek listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/write | Eszközök létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/encryptionSettings/read | A titkosítási beállítások listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/delete | A kiterjesztett tár adatainak törlése |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/delete | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/read | A kiterjesztett tár adatainak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/write | A kiterjesztett tár adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/write | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft.StorSimple/managers/features/read | A funkciók listázása |
> | Műveletek | Microsoft.StorSimple/managers/fileservers/read | A fájlkiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/getEncryptionKey/action | Az Eszközkezelő titkosítási kulcsának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/iscsiservers/read | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/jobs/read | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/listActivationKey/action | A StorSimple Eszközkezelő aktiválási kulcsának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Egy StorSimple Eszközkezelő nyilvános titkosítási kulcsainak listázása. |
> | Műveletek | Microsoft.StorSimple/managers/metrics/read | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Klasszikus áttelepítése a Jászolok Resource Managerbe |
> | Műveletek | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Áttelepítési forrás konfigurációinak listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Hozzon létre egy új felhőalapú készüléket. |
> | Műveletek | Microsoft.StorSimple/managers/read | Az eszközkezelők listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/Managers/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Műveletek | Microsoft.StorSimple/managers/regenerateActivationKey/action | Egy meglévő StorSimple Eszközkezelő aktiválási kulcsának újragenerálta. |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/delete | Törli a tárolási tartományokat |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/read | A tárolási tartományok listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/write | Tárolási tartományok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/write | Eszközkezelők létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/Managers/write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Műveletek | Microsoft.StorSimple/operations/read | A műveletek listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/register/action | Szolgáltató regisztrálása a Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics előfizetés kvótájának beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/operations/Read | Stream Analytics műveletek beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/Register/action | Előfizetés regisztrálása Stream Analytics erőforrás-szolgáltatóval |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics-feladatok törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics feladat funkciójának törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | A Stream Analytics Job függvény olvasási műveletének eredményei |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics feladat funkciójának beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics feladat funkciójának alapértelmezett definíciójának beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics feladat funkciójának tesztelése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Írási Stream Analytics feladat függvény |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics-feladathoz tartozó bevitel törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics-feladathoz való bevitel olvasási műveletének eredményei |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics-feladatba való bevitel beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Példa Stream Analytics feladatba való bevitelre |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics-feladathoz való bevitel tesztelése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Írási Stream Analytics feladatok bevitele |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Metrikus definíciók olvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics feladatokhoz tartozó olvasási művelet eredményei |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics feladatok kimenetének törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics feladatok kimenetének olvasási műveletének eredményei |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics a feladatok kimenetének olvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics feladatok kimenetének tesztelése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics a feladatok kimenetének írása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítás írása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/logDefinitions/READ | Lekéri a streamingjobs elérhető naplóit |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | A streamingjobs elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics-feladatok beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics feladatok indítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics feladatok leállítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics feladatok átalakításának törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics feladatok átalakításának olvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics feladatok átalakításának írása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analyticsi feladatok írása |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. előfizetés/megszakítás/művelet | Az előfizetés megszakítása |
> | Műveletek | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Műveletek | Microsoft.Subscription/register/action | Előfizetés regisztrálása a Microsoft. előfizetés erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. előfizetés/Átnevezés/művelet | Átnevezi az előfizetést |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/read | Azure-előfizetések definíciójának beszerzése egy felügyeleti csoporton belül. |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/write | Azure-előfizetés definíciójának létrehozása |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra. |
> | Műveletek | Microsoft.Support/supportTickets/read | Támogatási jegy részleteinek lekérése (beleértve az állapotot, súlyosságot, kapcsolattartási adatokat és kommunikációkat) vagy az összes előfizetés támogatási jegyeinek lekérése. |
> | Műveletek | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Törli a hozzáférési házirendet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hozzáférési szabályzat tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Új hozzáférési szabályzatot hoz létre egy környezethez, vagy frissít egy meglévő hozzáférési szabályzatot. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/delete | Törli a környezetet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Az eseményforrás törlése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/read | Egy eseményforrás tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Új eseményforrás létrehozása egy környezethez, vagy egy meglévő eseményforrás frissítése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/read | Egy környezet tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Törli a hivatkozási adatkészletet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Egy hivatkozási adathalmaz tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Új hivatkozási adatkészletet hoz létre egy adott környezethez, vagy frissít egy meglévő hivatkozási adatkészletet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/status/read | A környezet állapotának lekérése, a hozzá tartozó műveletek állapota, például bejövő adatok. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/write | Új környezet létrehozása vagy egy meglévő környezet frissítése. |
> | Műveletek | Microsoft.TimeSeriesInsights/register/action | Regisztrálja az előfizetést a Time Series Insights erőforrás-szolgáltatóhoz, és lehetővé teszi Time Series Insights környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Műveletek | Microsoft.VisualStudio/Account/Extension/Read | Fiók/bővítmény olvasása |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Read | Fiók/projekt olvasása |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Write | Fiók/projekt beállítása |
> | Műveletek | Microsoft.VisualStudio/Account/Read | Fiók beolvasása |
> | Műveletek | Microsoft.VisualStudio/Account/Write | Fiók beállítása |
> | Műveletek | Microsoft.VisualStudio/Extension/Delete | Bővítmény törlése |
> | Műveletek | Microsoft.VisualStudio/Extension/Read | Olvasási bővítmény |
> | Műveletek | Microsoft.VisualStudio/Extension/Write | Bővítmény beállítása |
> | Műveletek | Microsoft.VisualStudio/Project/Delete | Projekt törlése |
> | Műveletek | Microsoft.VisualStudio/Project/Read | Projekt beolvasása |
> | Műveletek | Microsoft.VisualStudio/Project/Write | Projekt beállítása |
> | Műveletek | Microsoft.VisualStudio/Register/Action | Azure-előfizetés regisztrálása a Microsoft. VisualStudio-szolgáltatóval |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.web/apimanagementaccounts/apiacls/read | Az API Management-fiókok Apiacls beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Az API Management-fiókok API-Apiacls törlése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/read | Az API Management-fiókok API-Apiacls beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/write | Az API Management-fiókok API-Apiacls frissítése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Az API Management-fiókok API-Connectionacls beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Erősítse meg a jóváhagyás kód API Management-fiókok API-kapcsolatait. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Az API Management-fiókok API-kapcsolatainak törlése Connectionacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management-fiókok API-kapcsolatok Connectionacls beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Az API Management-fiókok API-kapcsolatainak Connectionacls frissítése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management-fiókok API-kapcsolatainak törlése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Az API Management-fiókok API-kapcsolataihoz tartozó engedélyezési hivatkozások beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Kapcsolati kulcsok API Management-fiókok API-kapcsolatainak listázása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | A Secrets API Management-fiókok API-kapcsolatainak listázása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/read | API Management-fiókok API-kapcsolatainak beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/write | API Management-fiókok API-kapcsolatainak frissítése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/delete | Az API Management-fiókok API-k törlése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Az API Management-fiókok API-k honosított definícióinak törlése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Az API Management-fiókok API-k honosított definícióinak beolvasása. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Az API Management-fiókok API-k honosított definícióinak frissítése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/read | API Management-fiókok API-k beszerzése. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/write | Frissítse az API Management accounts API-kat. |
> | Műveletek | microsoft.web/apimanagementaccounts/connectionacls/read | Az API Management-fiókok Connectionacls beolvasása. |
> | Műveletek | Microsoft. Web/availablestacks/READ | Elérhető stackek beolvasása. |
> | Műveletek | Microsoft. Web/tanúsítványok/törlés | Meglévő tanúsítvány törlése. |
> | Műveletek | Microsoft. Web/tanúsítványok/olvasás | A tanúsítványok listájának beolvasása. |
> | Műveletek | Microsoft. Web/tanúsítványok/írás | Adjon hozzá egy új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Műveletek | Microsoft. Web/checknameavailability/READ | Ellenőrizze, hogy elérhető-e az erőforrás neve. |
> | Műveletek | Microsoft. Web/classicmobileservices/READ | Klasszikus Mobile Services beolvasása. |
> | Műveletek | Microsoft. Web/connectionGateways/delete | Egy összekötő átjáró törlése. |
> | Műveletek | Microsoft. Web/connectionGateways/csatlakozás/művelet | Kapcsolódási átjáróhoz csatlakozik. |
> | Műveletek | Microsoft. Web/connectionGateways/ListStatus/művelet | Egy összekötő átjáró állapotának felsorolása. |
> | Műveletek | Microsoft. Web/connectionGateways/Move/Action | Egy összekötő átjáró mozgatása. |
> | Műveletek | Microsoft. Web/connectionGateways/READ | A kapcsolatok átjáróinak listájának beolvasása. |
> | Műveletek | Microsoft. Web/connectionGateways/írás | Létrehoz vagy frissít egy összekötő átjárót. |
> | Műveletek | microsoft.web/connections/confirmconsentcode/action | Erősítse meg a kapcsolatok jóváhagyásának kódját. |
> | Műveletek | Microsoft. Web/Connections/delete | Töröl egy-kapcsolatokat. |
> | Műveletek | Microsoft. Web/kapcsolatok/csatlakozás/művelet | Csatlakozik a csatlakozáshoz. |
> | Műveletek | microsoft.web/connections/listconsentlinks/action | A kapcsolatok csatlakozási kapcsolatainak listázása. |
> | Műveletek | Microsoft. Web/Connections/Move/Action | Egy kapcsolatok áthelyezése. |
> | Műveletek | Microsoft. Web/Connections/READ | A kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft. Web/Connections/Write | Létrehozza vagy frissíti a kapcsolatokat. |
> | Műveletek | Microsoft.Web/customApis/Delete | Töröl egy egyéni API-t. |
> | Műveletek | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Az API-definíció kibontása egy WSDL-ből. |
> | Műveletek | Microsoft.Web/customApis/Join/Action | Csatlakozik egy egyéni API-hoz. |
> | Műveletek | Microsoft.Web/customApis/listWsdlInterfaces/Action | Az egyéni API WSDL-felületeit listázza. |
> | Műveletek | Microsoft.Web/customApis/Move/Action | Áthelyez egy egyéni API-t. |
> | Műveletek | Microsoft.Web/customApis/Read | Az egyéni API-k listájának beolvasása. |
> | Műveletek | Microsoft.Web/customApis/Write | Létrehoz vagy frissít egy egyéni API-t. |
> | Műveletek | Microsoft.Web/deletedSites/Read | Törölt webalkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/deploymentlocations/READ | Üzembe helyezési helyszínek beolvasása. |
> | Műveletek | Microsoft. Web/geoRegions/READ | A földrajzi régiók listájának beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/capacities/read | Üzemeltetési környezetek kapacitásainak beolvasása. |
> | Műveletek | Microsoft.Web/hostingEnvironments/Delete | App Service Environment törlése |
> | Műveletek | microsoft.web/hostingenvironments/detectors/read | Üzemeltetési környezetek érzékelők beszerzése. |
> | Műveletek | Microsoft. Web/hostingenvironments/diagnosztika/olvasás | Üzemeltetési környezetek diagnosztika beszerzése. |
> | Műveletek | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Az összes bejövő függőség hálózati végpontjának beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | Műveletek | microsoft.web/hostingenvironments/metricdefinitions/read | Üzemeltetési környezetek metrikai definícióinak beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Üzemeltetési környezetek többszerepköres készletek metrikai definícióinak beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/metrics/read | Üzemeltetési környezetek többszerepköres készletek metrikáinak beolvasása. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Előtér-készlet tulajdonságainak beolvasása egy App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/skus/read | Üzemeltetési környezetek többszerepköres készletek SKU-i beszerzése. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/usages/read | Üzemeltetési környezetek többszerepköres készletek használatának beolvasása. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Új előtér-készlet létrehozása App Service Environment vagy meglévő frissítése |
> | Műveletek | microsoft.web/hostingenvironments/operations/read | Üzemeltetési környezetek műveleteinek beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Az összes kimenő függőség hálózati végpontjának beolvasása. |
> | Műveletek | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Privát végponti kapcsolatok jóváhagyása |
> | Műveletek | Microsoft. Web/hostingEnvironments/READ | App Service Environment tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingEnvironments/reboot/Action | App Service Environment összes számítógépének újraindítása |
> | Műveletek | microsoft.web/hostingenvironments/resume/action | Üzemeltetési környezetek folytatása. |
> | Műveletek | microsoft.web/hostingenvironments/serverfarms/read | Üzemeltetési környezetek App Service csomagok beszerzése. |
> | Műveletek | microsoft.web/hostingenvironments/sites/read | Üzemeltetési környezetek Web Apps. |
> | Műveletek | microsoft.web/hostingenvironments/suspend/action | Üzemeltetési környezetek felfüggesztése. |
> | Műveletek | microsoft.web/hostingenvironments/usages/read | Üzemeltetési környezetek használatának beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Üzemeltetési környezetek Workerpools metrika-definíciók. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/metrics/read | Az üzemeltetési környezetek Workerpools metrikák beszerzése. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Read | Munkavégző készlet tulajdonságainak beolvasása egy App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/skus/read | Üzemeltetési környezetek beszerzése Workerpools SKU-ban. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/usages/read | Üzemeltetési környezetek Workerpools-használatok beszerzése. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Write | Új munkavégző készlet létrehozása egy App Service Environment vagy meglévő frissítése |
> | Műveletek | Microsoft. Web/hostingEnvironments/írás | Hozzon létre egy új App Service Environment vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. Web/ishostingenvironmentnameavailable/READ | Szerezze be, hogy elérhető-e az üzemeltetési környezet neve. |
> | Műveletek | Microsoft. Web/ishostnameavailable/READ | Ellenőrizze, hogy az állomásnév elérhető-e. |
> | Műveletek | Microsoft. Web/isusernameavailable/READ | Ellenőrizze, hogy elérhető-e a Felhasználónév. |
> | Műveletek | Microsoft.Web/listSitesAssignedToHostName/Read | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | Műveletek | microsoft.web/locations/apioperations/read | Locations API-műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/connectiongatewayinstallations/READ | Telephelyek közötti kapcsolatok átjárójának beolvasása. |
> | Műveletek | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet vagy alhálózati törlési értesítés a helyszínekről. |
> | Műveletek | microsoft.web/locations/extractapidefinitionfromwsdl/action | API-definíció kinyerése a WSDL-ből a helyekhez. |
> | Műveletek | microsoft.web/locations/listwsdlinterfaces/action | A helyükhöz tartozó WSDL-felületek listázása. |
> | Műveletek | microsoft.web/locations/managedapis/apioperations/read | Telephelyek által felügyelt API-műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/król/JOIN/Action | Egy felügyelt API-hoz csatlakozik. |
> | Műveletek | microsoft.web/locations/managedapis/read | Webhelyek által felügyelt API-k beolvasása. |
> | Műveletek | Microsoft. Web/Locations/operationResults/READ | Műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/Operations/READ | Műveletek beolvasása. |
> | Műveletek | microsoft.web/operations/read | Műveletek beolvasása. |
> | Műveletek | microsoft.web/publishingusers/read | Felhasználók közzétételének beolvasása. |
> | Műveletek | microsoft.web/publishingusers/write | A közzétételi felhasználók frissítése. |
> | Műveletek | Microsoft. Web/javaslatok/olvasás | Az előfizetésekre vonatkozó javaslatok listájának beolvasása. |
> | Műveletek | microsoft.web/register/action | Regisztrálja a Microsoft. web erőforrás-szolgáltatót az előfizetéshez. |
> | Műveletek | microsoft.web/resourcehealthmetadata/read | Resource Health metaadatok beolvasása. |
> | Műveletek | microsoft.web/serverfarms/capabilities/read | App Service csomagok funkcióinak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/Delete | Meglévő App Service csomag törlése |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/delete | Event Grid szűrő törlése a kiszolgálófarm esetében. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/READ | Event Grid szűrő beolvasása a kiszolgálófarm számára. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/írás | Helyezze Event Grid szűrőt a kiszolgálófarm számára. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service-csomagok törlése az első féltől származó alkalmazások beállításaival. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service-csomagok beszerzése az első féltől származó alkalmazások beállításait. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service-csomagok frissítése az első féltől származó alkalmazások beállításait. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/delete | Törölje App Service a hibrid kapcsolatok névtereit továbbító csomagok. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/READ | App Service csomagok hibrid kapcsolatok névterei továbbítók beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relays/Sites/READ | App Service csomagok hibrid kapcsolatok névtereit Web Apps. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service csomagok hibrid csatlakoztatási csomagokra vonatkozó korlátainak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionrelays/READ | App Service csomagok hibrid kapcsolatok továbbításának beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/metricdefinitions/READ | App Service csomagok metrika-definícióinak beolvasása. |
> | Műveletek | microsoft.web/serverfarms/metrics/read | App Service csomagok metrikáinak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/operationresults/READ | App Service csomagok műveleti eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/serverfarms/restartSites/Action | App Service-csomag összes Web Apps újraindítása |
> | Műveletek | microsoft.web/serverfarms/sites/read | App Service csomagok beszerzése Web Apps. |
> | Műveletek | microsoft.web/serverfarms/skus/read | App Service csomagok beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/használat/olvasás | App Service csomagok használatának beolvasása. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service csomagok frissítése Virtual Network kapcsolatok átjárók. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service csomagok beszerzése Virtual Network kapcsolatokhoz. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service csomagok törlése Virtual Network kapcsolatok útvonala. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service csomagok beszerzése Virtual Network kapcsolatok útvonala. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service csomagok frissítése Virtual Network kapcsolatok útvonala. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/Workers/újraindítás/művelet | App Service-csomagok újraindítása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/írás | Új App Service terv létrehozása vagy egy meglévő frissítése |
> | Műveletek | microsoft.web/sites/analyzecustomhostname/read | Egyéni állomásnév elemzése. |
> | Műveletek | Microsoft.Web/sites/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális webalkalmazásra |
> | Műveletek | Microsoft.Web/sites/backup/Action | Új webalkalmazás biztonsági másolatának létrehozása |
> | Műveletek | microsoft.web/sites/backup/read | Web Apps biztonsági másolat beolvasása. |
> | Műveletek | microsoft.web/sites/backup/write | Frissítse Web Apps biztonsági mentést. |
> | Műveletek | microsoft.web/sites/backups/action | Felfedi egy meglévő alkalmazás biztonsági mentését, amely az Azure Storage-beli blobból állítható vissza. |
> | Műveletek | microsoft.web/sites/backups/delete | Delete Web Apps Backups. |
> | Műveletek | microsoft.web/sites/backups/list/action | Web Apps biztonsági mentések listázása. |
> | Műveletek | Microsoft. Web/Sites/Backups/READ | Egy webalkalmazás biztonsági mentésének tulajdonságainak beolvasása |
> | Műveletek | microsoft.web/sites/backups/restore/action | Web Apps biztonsági mentések visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/Backups/Write | Web Apps biztonsági mentések frissítése. |
> | Műveletek | microsoft.web/sites/config/delete | Delete Web Apps Config. |
> | Műveletek | Microsoft.Web/sites/config/list/Action | A webalkalmazás biztonsági szempontból bizalmas beállításainak, például a hitelesítő adatok közzétételének, az Alkalmazásbeállítások és a kapcsolati karakterláncok listázása |
> | Műveletek | Microsoft.Web/sites/config/Read | Webalkalmazás konfigurációs beállításainak beolvasása |
> | Műveletek | microsoft.web/sites/config/snapshots/read | Web Apps konfigurációs Pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/config/Write | A webalkalmazás konfigurációs beállításainak frissítése |
> | Műveletek | microsoft.web/sites/containerlogs/action | A webalkalmazás tömörített tárolóinak naplóinak beolvasása. |
> | Műveletek | microsoft.web/sites/containerlogs/download/action | Web Apps tároló naplófájljainak letöltése. |
> | Műveletek | microsoft.web/sites/continuouswebjobs/delete | Delete Web Apps Continuous Web Jobs. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/READ | Web Apps folyamatos webes feladatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/Start/művelet | Indítsa el Web Apps folyamatos webes feladatokat. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/leállítás/művelet | A folyamatos webes feladatok Web Apps leállítása. |
> | Műveletek | Microsoft.Web/sites/Delete | Meglévő webalkalmazás törlése |
> | Műveletek | microsoft.web/sites/deployments/delete | Web Apps üzemelő példányok törlése. |
> | Műveletek | Microsoft. Web/Sites/üzembe helyezés/napló/olvasás | Web Apps központi telepítések naplójának beolvasása. |
> | Műveletek | microsoft.web/sites/deployments/read | Web Apps üzemelő példányok beolvasása. |
> | Műveletek | microsoft.web/sites/deployments/write | Web Apps központi telepítések frissítése. |
> | Műveletek | microsoft.web/sites/detectors/read | Web Apps érzékelők beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/analyses/execute/Action | Web Apps diagnosztikai elemzés futtatása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/elemzés/olvasás | Web Apps diagnosztika elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/aspnetcore/READ | Web Apps diagnosztika beszerzése ASP.NET Core alkalmazáshoz. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/autoheal/READ | Szerezze be Web Apps diagnosztika autohealt. |
> | Műveletek | Microsoft. Web/Sites/diagnosztika/üzembe helyezés/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/központi telepítések/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/detectors/execute/Action | Web Apps diagnosztikai detektor futtatása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/érzékelők/olvasás | Web Apps diagnosztikai detektor beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/failedrequestsperuri/READ | Web Apps diagnosztika sikertelen kérelmének beolvasása URI-n. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/frebanalysis/READ | Web Apps diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/loganalyzer/READ | Web Apps diagnosztikai napló Analizátorjának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/READ | Web Apps diagnosztikai kategóriák beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/runtimeavailability/READ | Web Apps diagnosztikai futtatókörnyezet rendelkezésre állásának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/servicehealth/READ | Web Apps diagnosztikai Service Health beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web Apps diagnosztikai hely CPU-elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitecrashes/READ | Web Apps diagnosztikai hely összeomlásának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitelatency/READ | Web Apps diagnosztikai hely késésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitememoryanalysis/READ | Web Apps diagnosztikai hely memória-elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siterestartsettingupdate/READ | Web Apps diagnosztikai hely újraindítási beállításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siterestartuserinitiated/READ | Web Apps diagnosztikai hely újraindítását kezdeményező felhasználó beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siteswap/READ | Web Apps diagnosztikai hely felcserélése. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/ThreadCount/READ | Web Apps diagnosztikai szálak számának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/workeravailability/READ | Web Apps diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/workerprocessrecycle/READ | Web Apps diagnosztika munkavégző folyamatának újrahasznosítása. |
> | Műveletek | microsoft.web/sites/domainownershipidentifiers/read | Web Apps tartomány tulajdonjog-azonosítóinak beolvasása. |
> | Műveletek | microsoft.web/sites/domainownershipidentifiers/write | Web Apps tartomány tulajdonjog-azonosítóinak frissítése. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/delete | Event Grid szűrő törlése a webalkalmazásban. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/READ | Event Grid szűrő beolvasása a webalkalmazásban. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/Write | Event Grid szűrő elhelyezése a webalkalmazásban. |
> | Műveletek | microsoft.web/sites/extensions/delete | Web Apps hely bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/Extensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Extensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Műveletek | microsoft.web/sites/functions/action | Functions Web Apps. |
> | Műveletek | microsoft.web/sites/functions/delete | Web Apps függvények törlése. |
> | Műveletek | Microsoft. Web/Sites/functions/kulcsok/törlés | Funkcióbillentyűk törlése. |
> | Műveletek | Microsoft. Web/webhelyek/függvények/kulcsok/írás | Funkcióbillentyűk frissítése. |
> | Műveletek | Microsoft. Web/Sites/functions/listkeys műveletének beolvasása/Action | A függvények kulcsainak listázása. |
> | Műveletek | microsoft.web/sites/functions/listsecrets/action | A függvények titkainak listázása. |
> | Műveletek | Microsoft. Web/Sites/functions/MasterKey/READ | Web Apps függvények MasterKey beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Properties/READ | Web Apps függvények tulajdonságainak olvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Properties/Write | Web Apps függvények tulajdonságainak frissítése. |
> | Műveletek | Microsoft. Web/Sites/functions/READ | Web Apps függvények beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/token/READ | Web Apps functions token beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Write | Web Apps függvények frissítése. |
> | Műveletek | Microsoft. Web/Sites/Host/functionkeys/delete | A functions Host funkcióbillentyűk törlése. |
> | Műveletek | Microsoft. Web/Sites/Host/functionkeys/Write | Functions Host-funkcióbillentyűk frissítése. |
> | Műveletek | Microsoft. Web/Sites/Host/listkeys műveletének beolvasása/művelet | Listázza a functions gazdagép kulcsait. |
> | Műveletek | Microsoft. Web/Sites/Host/listsyncstatus/művelet | A szinkronizálási függvény eseményindítóinak listázása. |
> | Műveletek | Microsoft. Web/Sites/Host/Properties/READ | Olvassa el Web Apps függvények gazdagépének tulajdonságait. |
> | Műveletek | Microsoft. Web/Sites/Host/Sync/Action | A szinkronizálási függvények eseményindítói. |
> | Műveletek | Microsoft. Web/Sites/Host/systemkeys/delete | A functions gazdagép rendszerkulcsainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Host/systemkeys/Write | A functions gazdagép rendszerkulcsainak frissítése. |
> | Műveletek | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostruntime/functions/keys/read | Get Web Apps Hostruntime Functions Keys. |
> | Műveletek | Microsoft.Web/sites/hostruntime/host/_master/read | függvényalkalmazás fő kulcsának beolvasása rendszergazdai műveletekhez |
> | Műveletek | Microsoft.Web/sites/hostruntime/host/action | Hajtsa végre függvényalkalmazás futtatókörnyezet műveleteit, például a Szinkronizáló eseményindítókat, a függvények hozzáadását, a függvények meghívását, a függvények törlését |
> | Műveletek | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Műveletek | microsoft.web/sites/hybridconnection/delete | Web Apps hibrid kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnection/READ | Web Apps hibrid kapcsolatok beszerzése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnection/Write | Web Apps hibrid kapcsolatok frissítése. |
> | Műveletek | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Törölje Web Apps hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/listkeys műveletének beolvasása/Action | Kulcsok listázása Web Apps hibrid kapcsolatok névterei továbbítók. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/READ | Web Apps hibrid kapcsolatok névterei továbbításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionrelays/READ | Web Apps hibrid kapcsolatok továbbításának beolvasása. |
> | Műveletek | microsoft.web/sites/instances/deployments/delete | Web Apps példányok telepítésének törlése. |
> | Műveletek | microsoft.web/sites/instances/deployments/read | Web Apps példányok üzembe helyezésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/Extensions/log/READ | Web Apps példányok bővítmények naplójának beolvasása. |
> | Műveletek | microsoft.web/sites/instances/extensions/processes/read | Web Apps példányok bővítmények folyamatainak beolvasása. |
> | Műveletek | microsoft.web/sites/instances/extensions/read | Web Apps példányok bővítményeinek beolvasása. |
> | Műveletek | microsoft.web/sites/instances/processes/delete | Web Apps példányok folyamatainak törlése. |
> | Műveletek | microsoft.web/sites/instances/processes/modules/read | Web Apps példányok beolvasása modulokat dolgoz fel. |
> | Műveletek | microsoft.web/sites/instances/processes/read | Web Apps példányok folyamatainak beolvasása. |
> | Műveletek | microsoft.web/sites/instances/processes/threads/read | Web Apps példányok beolvasása szálakat dolgoz fel. |
> | Műveletek | microsoft.web/sites/instances/read | Web Apps példányok beolvasása. |
> | Műveletek | microsoft.web/sites/listsyncfunctiontriggerstatus/action | A szinkronizálási függvény trigger állapotának listázása. |
> | Műveletek | Microsoft. Web/Sites/metricdefinitions/READ | Web Apps metrikák definícióinak beolvasása. |
> | Műveletek | microsoft.web/sites/metrics/read | Web Apps mérőszámok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/metricsdefinitions/READ | Web Apps mérőszámok definícióinak beolvasása. |
> | Műveletek | microsoft.web/sites/migratemysql/action | Telepítse át a MySql Web Apps. |
> | Műveletek | microsoft.web/sites/migratemysql/read | A MySql Web Apps migrálása. |
> | Műveletek | microsoft.web/sites/networktrace/action | Hálózati nyomkövetési Web Apps. |
> | Műveletek | Microsoft. Web/Sites/networktraces/operationresults/READ | Web Apps hálózati nyomkövetési művelet eredményeinek beolvasása. |
> | Műveletek | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Műveletek | Microsoft. Web/Sites/operationresults/READ | Web Apps művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Operations/READ | Web Apps műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/perfcounters/READ | Web Apps teljesítményszámlálók beolvasása. |
> | Műveletek | microsoft.web/sites/premieraddons/delete | Web Apps Premier bővítmények törlése. |
> | Műveletek | Microsoft. Web/Sites/premieraddons/READ | Web Apps Premier addons beszerzése. |
> | Műveletek | Microsoft. Web/Sites/premieraddons/Write | Web Apps Premier addons frissítése. |
> | Műveletek | microsoft.web/sites/privateaccess/read | A webhelyhez hozzáférő, a webhely elérését engedélyező és engedélyezett virtuális hálózatok körének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/Action | Privát végponti kapcsolatok jóváhagyása |
> | Műveletek | microsoft.web/sites/processes/modules/read | Web Apps folyamatok moduljainak beolvasása. |
> | Műveletek | microsoft.web/sites/processes/read | Web Apps folyamatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/processs/Threads/READ | Web Apps folyamatok szálának beolvasása. |
> | Műveletek | microsoft.web/sites/publiccertificates/delete | Web Apps nyilvános tanúsítványok törlése. |
> | Műveletek | microsoft.web/sites/publiccertificates/read | Web Apps nyilvános tanúsítványok beszerzése. |
> | Műveletek | microsoft.web/sites/publiccertificates/write | Web Apps nyilvános tanúsítványok frissítése. |
> | Műveletek | Microsoft.Web/sites/publish/Action | Webalkalmazás közzététele |
> | Műveletek | Microsoft.Web/sites/publishxml/Action | Webes alkalmazás közzétételi profiljának XML-kódjának beolvasása |
> | Műveletek | microsoft.web/sites/publishxml/read | Web Apps közzétételi XML-fájl beolvasása. |
> | Műveletek | Microsoft. Web/Sites/READ | Webalkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/recommendationhistory/READ | Web Apps javaslat előzményeinek beolvasása. |
> | Műveletek | microsoft.web/sites/recommendations/disable/action | Web Apps javaslatok letiltása. |
> | Műveletek | Microsoft. Web/Sites/javaslatok/olvasás | A webalkalmazáshoz tartozó javaslatok listájának beolvasása. |
> | Műveletek | microsoft.web/sites/recover/action | Recover Web Apps. |
> | Műveletek | Microsoft.Web/sites/resetSlotConfig/Action | Webalkalmazás-konfiguráció alaphelyzetbe állítása |
> | Műveletek | microsoft.web/sites/resourcehealthmetadata/read | Web Apps Resource Health metaadatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/restart/Action | Webalkalmazás újraindítása |
> | Műveletek | microsoft.web/sites/restore/read | Web Apps visszaállítás beolvasása. |
> | Műveletek | microsoft.web/sites/restore/write | Web Apps visszaállítása. |
> | Műveletek | microsoft.web/sites/restorefrombackupblob/action | A webalkalmazás visszaállítása a biztonsági mentési Blobból. |
> | Műveletek | Microsoft. Web/Sites/restorefromdeletedapp/Action | Web Apps visszaállítása a törölt alkalmazásból. |
> | Műveletek | microsoft.web/sites/restoresnapshot/action | Web Apps Pillanatképek visszaállítása. |
> | Műveletek | microsoft.web/sites/siteextensions/delete | Web Apps hely bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/siteextensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/siteextensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Műveletek | microsoft.web/sites/slots/analyzecustomhostname/read | Web Apps tárolóhelyek elemzése az egyéni állomásnév elemzéséhez. |
> | Műveletek | Microsoft.Web/sites/slots/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális bővítőhelyre. |
> | Műveletek | Microsoft.Web/sites/slots/backup/Action | Hozzon létre új webalkalmazás-tárolóhely biztonsági mentést. |
> | Műveletek | microsoft.web/sites/slots/backup/read | Web Apps tárolóhelyek biztonsági másolatának beolvasása. |
> | Műveletek | microsoft.web/sites/slots/backup/write | Web Apps tárolóhelyek biztonsági mentésének frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/művelet | Fedezze fel Web Apps bővítőhelyek biztonsági másolatait. |
> | Műveletek | microsoft.web/sites/slots/backups/delete | Web Apps tárolóhelyek biztonsági mentésének törlése. |
> | Műveletek | microsoft.web/sites/slots/backups/list/action | Web Apps tárolóhelyek biztonsági másolatainak listázása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/READ | Webalkalmazási tárolóhelyek biztonsági mentésének tulajdonságainak beolvasása |
> | Műveletek | microsoft.web/sites/slots/backups/restore/action | Web Apps tárolóhelyek biztonsági másolatának visszaállítása. |
> | Műveletek | microsoft.web/sites/slots/config/delete | Web Apps tárolóhelyek konfigurációjának törlése. |
> | Műveletek | Microsoft.Web/sites/slots/config/list/Action | A webalkalmazási tárolóhely biztonsági kényes beállításainak listázása, például a hitelesítő adatok közzététele, az Alkalmazásbeállítások és a kapcsolati karakterláncok |
> | Műveletek | Microsoft.Web/sites/slots/config/Read | Webalkalmazási tárolóhely konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/config/Write | A webalkalmazási tárolóhely konfigurációs beállításainak frissítése |
> | Műveletek | microsoft.web/sites/slots/containerlogs/action | A webalkalmazások tárolóhelyének tömörített tároló-naplófájljainak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/containerlogs/download/action | Töltse le Web Apps tárolóhelyek tárolójának naplóit. |
> | Műveletek | microsoft.web/sites/slots/continuouswebjobs/delete | Web Apps tárolóhelyek folyamatos webes feladatának törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/READ | Web Apps tárolóhelyek folyamatos webes feladatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/Start/művelet | Indítsa el Web Apps bővítőhelyek folyamatos webes feladatait. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/leállítás/művelet | Állítsa le Web Apps résidők folyamatos webes feladatait. |
> | Műveletek | Microsoft.Web/sites/slots/Delete | Meglévő webalkalmazási tárolóhely törlése |
> | Műveletek | microsoft.web/sites/slots/deployments/delete | Web Apps tárolóhelyek telepítésének törlése. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/üzembe helyezés/napló/olvasás | Web Apps tárolóhelyek üzembe helyezési naplójának beolvasása. |
> | Műveletek | microsoft.web/sites/slots/deployments/read | Web Apps tárolóhelyek központi telepítésének beolvasása. |
> | Műveletek | microsoft.web/sites/slots/deployments/write | Web Apps bővítőhelyek telepítésének frissítése. |
> | Műveletek | microsoft.web/sites/slots/detectors/read | Web Apps tárolóhelyek észlelésének beolvasása. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Futtassa Web Apps bővítőhely diagnosztikai elemzését. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/elemzés/olvasás | Web Apps tárolóhelyek diagnosztikai elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/READ | ASP.NET Core alkalmazáshoz Web Apps bővítőhely diagnosztika beszerzése. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/autoheal/olvasás | Szerezze be a Web Apps bővítőhely diagnosztika autoheal szolgáltatását. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/üzembe helyezés/olvasás | Szerezze be Web Apps tárolóhelyek diagnosztika üzembe helyezését. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/központi telepítés/olvasás | Web Apps tárolóhelyek diagnosztikai telepítésének beolvasása. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web Apps bővítőhely diagnosztikai detektor futtatása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/érzékelők/olvasás | Web Apps tárolóhelyek diagnosztikai Detektorának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/frebanalysis/READ | Web Apps bővítőhelyek diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/READ | Web Apps tárolóhelyek diagnosztikai naplójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/olvasás | Web Apps tárolóhelyek diagnosztika beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/READ | Szerezze be Web Apps tárolóhelyek diagnosztika-futtatókörnyezetének rendelkezésre állását. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/READ | Szerezze be Web Apps bővítőhelyek diagnosztikai Service Healthét. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének CPU-elemzését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/READ | A Web Apps tárolóhelyek diagnosztikai helyének összeomlása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitelatency/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének késését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének memóriájának elemzését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/READ | Web Apps tárolóhelyek diagnosztikai helyének újraindítása a frissítés beállításának beolvasása. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web Apps tárolóhelyek diagnosztikai helyének újraindítási felhasználójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/READ | Web Apps tárolóhelyek diagnosztikai helyének felcserélése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/READ | Web Apps bővítőhelyek diagnosztikai szálak számának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/workeravailability/READ | Web Apps bővítőhely diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/READ | Szerezze be Web Apps bővítőhely diagnosztika munkavégző folyamatának újrahasznosítását. |
> | Műveletek | microsoft.web/sites/slots/domainownershipidentifiers/read | Web Apps bővítőhelyek tartományi tulajdonosi azonosítóinak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/functions/listsecrets/action | A Secrets Web Apps a tárolóhelyek funkcióinak listázása. |
> | Műveletek | Microsoft. Web/Sites/Slots/functions/READ | Web Apps tárolóhelyek funkcióinak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/hostnamebindings/delete | Delete Web Apps Slots Hostname Bindings. |
> | Műveletek | microsoft.web/sites/slots/hostnamebindings/read | Get Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft. Web/Sites/Slots/hostnamebindings/Write | Update Web Apps Slots Hostname Bindings. |
> | Műveletek | microsoft.web/sites/slots/hybridconnection/delete | Web Apps bővítőhelyek hibrid kapcsolatainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnection/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnection/Write | Web Apps bővítőhelyek hibrid kapcsolatainak frissítése. |
> | Műveletek | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Törölje Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnectionrelays/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/instances/deployments/read | Web Apps bővítőhely példányok üzembe helyezésének beolvasása. |
> | Műveletek | microsoft.web/sites/slots/instances/processes/delete | Web Apps tárolóhely-példányok folyamatainak törlése. |
> | Műveletek | microsoft.web/sites/slots/instances/processes/read | Web Apps résidő-példányok folyamatainak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/instances/read | Web Apps tárolóhelyek példányainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/metricdefinitions/READ | Web Apps bővítőhelyek metrikai definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/slot/metrika/olvasás | Web Apps bővítőhely metrikáinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/migratemysql/READ | Web Apps bővítőhelyek áttelepíthetők a MySql-re. |
> | Műveletek | microsoft.web/sites/slots/networktrace/action | Hálózati nyomkövetési Web Apps tárolóhelyek. |
> | Műveletek | Microsoft. Web/Sites/Slots/networktraces/operationresults/READ | Web Apps tárolóhelyek hálózati nyomkövetési műveletének eredményeinek beolvasása. |
> | Műveletek | microsoft.web/sites/slots/newpassword/action | ÚjJelszó Web Apps tárolóhelyek. |
> | Műveletek | Microsoft. Web/Sites/Slots/operationresults/READ | Web Apps bővítőhelyek műveleti eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Operations/READ | Web Apps tárolóhelyek műveleteinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/perfcounters/READ | Web Apps bővítőhelyek teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/phplogging/READ | Web Apps bővítőhelyek Phplogging beolvasása. |
> | Műveletek | microsoft.web/sites/slots/premieraddons/delete | Web Apps bővítőhely Premier addons törlése. |
> | Műveletek | microsoft.web/sites/slots/premieraddons/read | Web Apps Slots Premier addons beszerzése. |
> | Műveletek | Microsoft. Web/Sites/Slots/premieraddons/Write | Web Apps Slots Premier addons frissítése. |
> | Műveletek | microsoft.web/sites/slots/processes/read | Web Apps tárolóhelyek folyamatainak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/delete | Web Apps bővítőhely nyilvános tanúsítványainak törlése. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/read | Web Apps bővítőhely nyilvános tanúsítványainak beolvasása. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/write | Web Apps bővítőhely nyilvános tanúsítványainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Web/sites/slots/publish/Action | Webalkalmazási tárolóhely közzététele |
> | Műveletek | Microsoft.Web/sites/slots/publishxml/Action | Közzétételi profil XML-kódjának beolvasása a webalkalmazási tárolóhelyhez |
> | Műveletek | Microsoft. Web/Sites/Slots/READ | Webalkalmazás-telepítési tárolóhely tulajdonságainak beolvasása |
> | Műveletek | microsoft.web/sites/slots/recover/action | Web Apps tárolóhelyek helyreállítása. |
> | Műveletek | Microsoft.Web/sites/slots/resetSlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alaphelyzetbe állítása |
> | Műveletek | microsoft.web/sites/slots/resourcehealthmetadata/read | Web Apps tárolóhelyek Resource Health metaadatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/restart/Action | Webalkalmazás-tárolóhely újraindítása |
> | Műveletek | Microsoft. Web/Sites/slot/Restore/READ | Web Apps tárolóhelyek visszaállításának beolvasása. |
> | Műveletek | microsoft.web/sites/slots/restore/write | Web Apps tárolóhelyek visszaállítása. |
> | Műveletek | microsoft.web/sites/slots/restorefrombackupblob/action | Web Apps tárolóhely visszaállítása a biztonsági mentési Blobból. |
> | Műveletek | Microsoft. Web/Sites/Slots/restorefromdeletedapp/Action | Webalkalmazás-tárolóhelyek visszaállítása a törölt alkalmazásból. |
> | Műveletek | microsoft.web/sites/slots/restoresnapshot/action | Web Apps tárolóhelyek pillanatképének visszaállítása. |
> | Műveletek | microsoft.web/sites/slots/siteextensions/delete | Web Apps bővítőhelyek helyének bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/siteextensions/READ | Web Apps bővítőhelyek helyének bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/siteextensions/Write | Frissítse Web Apps bővítőhelyek helyének bővítményeit. |
> | Műveletek | Microsoft. Web/Sites/Slots/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Műveletek | microsoft.web/sites/slots/snapshots/read | Web Apps tárolóhelyek pillanatképének beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Delete | Webalkalmazás-tárolóhely forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Műveletek | Microsoft. Web/Sites/Slots/sourcecontrols/READ | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/sourcecontrols/Write | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/sites/slots/start/Action | Webalkalmazás-tárolóhely elindítása |
> | Műveletek | Microsoft.Web/sites/slots/stop/Action | Webalkalmazási tárolóhely leállítása |
> | Műveletek | microsoft.web/sites/slots/sync/action | Web Apps tárolóhelyek szinkronizálása. |
> | Műveletek | microsoft.web/sites/slots/triggeredwebjobs/delete | A webjobs által aktivált Web Apps tárolóhelyek törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/triggeredwebjobs/READ | Beolvashatja Web Apps bővítőhelyek által aktivált webjobs-feladatokat. |
> | Műveletek | microsoft.web/sites/slots/triggeredwebjobs/run/action | Futtassa Web Apps bővítőhelyen indított webjobs-feladatokat. |
> | Műveletek | Microsoft. Web/Sites/Slots/használati adatok/olvasás | Web Apps tárolóhelyek használatának beolvasása. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web Apps bővítőhelyek Virtual Network kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/slot/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps tárolóhelyek Virtual Network kapcsolatok átjáróit. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/read | Web Apps bővítőhelyek Virtual Network kapcsolatok beszerzése. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/write | Web Apps bővítőhelyek Virtual Network kapcsolatok frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/webjobs/olvasás | Web Apps slot webjobs beszerzése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Write | Hozzon létre egy új webalkalmazási tárolóhelyet vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. Web/Sites/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Műveletek | Microsoft.Web/sites/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Műveletek | microsoft.web/sites/snapshots/read | Web Apps Pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Delete | Webalkalmazás forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Műveletek | Microsoft. Web/Sites/sourcecontrols/READ | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/sourcecontrols/Write | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/sites/start/Action | Webalkalmazás indítása |
> | Műveletek | Microsoft.Web/sites/stop/Action | Webalkalmazás leállítása |
> | Műveletek | microsoft.web/sites/sync/action | Sync Web Apps. |
> | Műveletek | Microsoft. Web/Sites/syncfunctiontriggers/Action | A szinkronizálási függvények eseményindítói. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/delete | Web Apps indított webjobs-feladatok törlése. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/előzmények/olvasás | Web Apps indított webjobs-előzmények beolvasása. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/READ | Web Apps indított webjobs-feladatok beolvasása. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/run/action | Web Apps indított webjobs-feladatok futtatása. |
> | Műveletek | Microsoft. Web/Sites/használat/olvasás | Web Apps használati adatok beolvasása. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/delete | Web Apps Virtual Network kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/READ | Web Apps Virtual Network kapcsolatok átjáróinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps Virtual Network Connections átjárókat. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/read | Web Apps Virtual Network kapcsolatok beolvasása. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/write | Web Apps Virtual Network kapcsolatok frissítése. |
> | Műveletek | Microsoft. Web/Sites/webjobs/olvasás | Web Apps webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Write | Hozzon létre egy új webalkalmazást, vagy frissítsen egy meglévőt |
> | Műveletek | microsoft.web/skus/read | SKU-i beolvasása. |
> | Műveletek | Microsoft. Web/sourcecontrols/READ | Forrás vezérlők beolvasása. |
> | Műveletek | Microsoft. Web/sourcecontrols/írás | Frissítse a forrás vezérlőelemeket. |
> | Műveletek | microsoft.web/unregister/action | A Microsoft. web erőforrás-szolgáltató regisztrációjának törlése az előfizetéshez. |
> | Műveletek | microsoft.web/validate/action | Érvényesít. |
> | Műveletek | microsoft.web/verifyhostingenvironmentvnet/action | Az üzemeltetési környezet vnet ellenőrzése. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. WorkloadMonitor/összetevők/olvasás | Az erőforrás összetevőinek beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/componentsSummary/read | Az összetevők összegzésének beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/monitorInstances/READ | Az erőforráshoz tartozó figyelők példányainak beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | A figyelő példányok összegzésének beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/figyelők/olvasás | Az erőforráshoz tartozó figyelők beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/figyelők/írás | Az erőforráshoz tartozó figyelő konfigurálása |
> | Műveletek | Microsoft.WorkloadMonitor/notificationSettings/read | Értesítési beállítások beolvasása az erőforráshoz |
> | Műveletek | Microsoft. WorkloadMonitor/notificationSettings/Write | Az erőforráshoz tartozó értesítési beállítások konfigurálása |
> | Műveletek | Microsoft. WorkloadMonitor/Operations/READ | A támogatott műveletek beolvasása |

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/azure-services-resource-providers.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
