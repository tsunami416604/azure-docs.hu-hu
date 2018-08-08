---
title: Az Azure Resource Manager erőforrás-szolgáltatói műveletek |} A Microsoft Docs
description: A Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a4e062b1bc56eada2fa2c27797151e265271022e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621157"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Az Azure Resource Manager erőforrás-szolgáltatói műveletek

Ez a cikk a minden Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása. Ezek a műveletek használható [egyéni szerepkörök](custom-roles.md) biztosít részletes [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) erőforrásokhoz az Azure-ban. A művelet karakterláncok formátuma a következő: `{Company}.{ProviderName}/{resourceType}/{action}`

Az erőforrás-szolgáltatói műveletek mindig folyamatosan fejlődik. A legújabb műveletek beolvasásához használja [Get-azurermprovideroperation Parancsmagból](/powershell/module/azurerm.resources/get-azurermprovideroperation) vagy [az művelet lista](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AAD/domainServices/delete | Tartományi szolgáltatás törlése |
> | Műveletek | Microsoft.AAD/domainServices/read | Olvasási tartományi szolgáltatás |
> | Műveletek | Microsoft.AAD/domainServices/write | Tartományi szolgáltatások írása |
> | Műveletek | Microsoft.AAD/locations/operationresults/read |  |
> | Műveletek | Microsoft.AAD/Operations/read |  |
> | Műveletek | Microsoft.AAD/register/action | Tartományi szolgáltatás regisztrálása |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/DELETE | Diagnosztikai beállítás törlése |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/Read | Diagnosztikai beállítás beolvasása |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/Write | Diagnosztikai beállítás írása |
> | Műveletek | Microsoft.aadiam/diagnosticsettingscategories/Read | A diagnosztikai beállítás kategóriák olvasása |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | A bérlők számára elérhető naplók beolvasása |

## <a name="microsoftaddons"></a>Mirosoft.addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Addons/operations/read | Lekérdezi az RP-műveletek támogatott. |
> | Műveletek | Microsoft.Addons/register/action | A megadott előfizetés regisztrálása a Mirosoft.addons |
> | Műveletek | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Megjeleníti a megadott előfizetés az aktuális támogatási csomag adatait. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott Canonical-támogatási csomag |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/read | A megadott Canonical-támogatási terv állapot beolvasása. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/write | A Canonical-támogatási csomag megadott típus hozzáadása. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/action | Hozzon létre új erdőt a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Minden kiszolgáló lekéri a megadott szolgáltatásnév. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/alerts/read | Riasztások részleteinek beolvasása az erdő például alertid, riasztás kiváltása dátum, a riasztás utolsó észlelt, a riasztás leírásában, az utolsó frissíthetők, a riasztási szint, riasztási állapot, riasztás hibaelhárítási hivatkozások stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/configuration/read | Az erdő szolgáltatás konfigurációjának beolvasása. Példa - erdő neve, Functionla szintje, a tartomány-kiosztási főkiszolgáló műveleti Főkiszolgálói szerepkörének, séma-főkiszolgáló műveleti Főkiszolgálói szerepkör stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/delete | Szolgáltatás törlése és azt a kiszolgálók egészségügyi adatokkal együtt. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Tartományok és helyek részleteinek beolvasása az erdő számára. Példa-állapot, aktív riasztások, megoldott riasztások, a tulajdonságokat, mint a tartomány működési szintjét, erdő, infrastruktúra-főkiszolgáló, elsődleges tartományvezérlő, relatív azonosítókat kezelő főkiszolgáló stb.  |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Az erdő felhasználói szabályozó beállításának beolvasása.<br>Példa – például ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches MetricCounterName.<br>Beállításai, a felhasználói felület diagramok stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Erdő például erdő neve, ebben az erdőben a tartományok, helyek és a helyek részletek stb száma az adott erdőben összefoglaló beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Lekérdezi a támogatott mérőszámok listája egy adott szolgáltatáshoz.<br>A példában a fiók zárolásának extranetes, összes sikertelen kérelem, szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), jogkivonat-kérelmeket/sec AD FS szolgáltatás stb.<br>Az NTLM-hitelesítések/mp, LDAP sikeres kötések/mp, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, Insights Agent a saját memória, exportálási statisztikák az Azure AD-hez ADSync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Adja meg a szolgáltatás, az API-t lekéri a metrikák adatait.<br>Például az API segítségével kapcsolatos információk: extranetes a fiók zárolásának, a sikertelen kérelmek teljes, a szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítések/mp, LDAP sikeres kötések/mp-ben, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomain szolgáltatás.<br>Profil késés, a TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Az API-t egy premium-bérlő összes előkészítve ADDomainServices listájának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/read | A szolgáltatás részletei lekéri a megadott szolgáltatásnév. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | A megadott szolgáltatásnév számára a kiszolgálók replikálási adatainak beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Lekérdezi a tartományvezérlők és a replikációs hibákat, ha van ilyen. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Lekérdezi és replikálási adatainak az adott erdőben a tartománynév-vezérlő listája befejezéséhez. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adja hozzá a szolgáltatás egy kiszolgálópéldányt. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Az API-t során ADDomainService kiszolgálóregisztrációról, a hitelesítő adatainak lekérése bevezetése új kiszolgálók neve. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Törli a kiszolgálót, egy adott szolgáltatás és a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/write | Létrehozza vagy frissíti a ADDomainService példány a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/action | Frissítések a bérlő konfigurációjához. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/read | Olvassa be a bérlő konfigurációjához. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/write | A bérlői konfigurációt hoz létre. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/contents/read | Az ügynök telepítése és a regisztrációs naplókat tárolja a blob tartalmának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/read | Az ügynök telepítése és a regisztrációs naplókat lekérdezi a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/operations/read | Rendszer által támogatott műveletek listájának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/register/action | Állapotfigyelő szolgáltatás ADHybrid erőforrás-szolgáltató regisztrálása, és lehetővé teszi az állapotfigyelő szolgáltatás ADHybrid erőforrás létrehozását. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Támogatja az ügyfél incidensek DevOps segítségével rendelkezésre álló régiók listájának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassword/read | Helytelen jelszómegadási kísérletek listájának beolvasása az Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Beolvassa a Blob SAS URI-t tartalmazó állapotát, és / UserId / IP-cím egy adott bérlő esetében naponta megkísérli a rossz felhasználónévvel/jelszóval gyakorisága újonnan várólistán lévő jelentés feladat végső eredménye. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Fejlesztési és üzemeltetési listájának beolvasása által jóváhagyott bérlők számára. Általában ügyfél-támogatási használják. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/isdevops/read | Időjárási a teannt az fejlesztési és üzemeltetési által jóváhagyott, vagy nem jelző érték beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | A kiválasztott fejlesztési ops bérlő userid(objectid) frissítéseket. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Lekérdezi kiválasztott központi telepítésére az adott bérlő esetében. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | A bérlő azonosítóját megadott lekérdezi a bérlő tárolási helyét. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Lekérdezi a földrajzi hely, ahonnan adatokat fogják elérni. |
> | Műveletek | Microsoft.ADHybridHealthService/services/action | A bérlő szolgáltatáspéldány frissíti. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatás a riasztások beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatás a riasztások beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Egy szolgáltatás a megadott neve ellenőrzi egy szolgáltatás rendelkezik-e mindent, hogy a szolgáltatás használatához szükséges. |
> | Műveletek | Microsoft.ADHybridHealthService/services/delete | Törli a bérlő szolgáltatáspéldány. |
> | Műveletek | Microsoft.ADHybridHealthService/services/exporterrors/read | Az exportálási hibák beolvasása egy adott szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/exportstatus/read | Az Exportálás állapota lekéri egy adott szolgáltatáshoz. |
> | Műveletek | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Riasztások visszajelzés lekéri egy adott szolgáltatás és -kiszolgálót. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metricmetadata/read | Lekérdezi a támogatott mérőszámok listája egy adott szolgáltatáshoz.<br>A példában a fiók zárolásának extranetes, összes sikertelen kérelem, szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), jogkivonat-kérelmeket/sec AD FS szolgáltatás stb.<br>Az NTLM-hitelesítések/mp, LDAP sikeres kötések/mp, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, Insights Agent a saját memória, exportálási statisztikák az Azure AD-hez ADSync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Adja meg a szolgáltatás, az API-t az átlag az adott szolgáltatás metrikáinak beolvasása.<br>Például az API segítségével kapcsolatos információk: extranetes a fiók zárolásának, a sikertelen kérelmek teljes, a szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítések/mp, LDAP sikeres kötések/mp-ben, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomain szolgáltatás.<br>Profil késés, a TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/read | Adja meg a szolgáltatás, az API-t lekéri a metrikák adatait.<br>Például az API segítségével kapcsolatos információk: extranetes a fiók zárolásának, a sikertelen kérelmek teljes, a szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítések/mp, LDAP sikeres kötések/mp-ben, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomain szolgáltatás.<br>Profil késés, a TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Adja meg a szolgáltatás, az API lekérdezi a metrikák adott szolgáltatás összesített nézetet.<br>Például az API segítségével kapcsolatos információk: extranetes a fiók zárolásának, a sikertelen kérelmek teljes, a szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítések/mp, LDAP sikeres kötések/mp-ben, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomain szolgáltatás.<br>Profil késés, a TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adja hozzá, vagy egy szolgáltatás figyelési konfigurációjának frissítése. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Lekéri egy adott szolgáltatáshoz a figyelési konfigurációhoz. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adja hozzá, vagy frissíti a figyelési konfigurációk a szolgáltatáshoz. |
> | Műveletek | Microsoft.ADHybridHealthService/services/premiumcheck/read | Az API-t egy premium-bérlő összes előkészítve szolgáltatások listájának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/read | Beolvassa a szolgáltatáspéldányok a bérlőben. |
> | Műveletek | Microsoft.ADHybridHealthService/services/reports/details/read | Lekérdezi a jelentés az elmúlt 7 nap a helytelen jelszó típusú hiba a legtöbbet próbálkozó 50 felhasználó |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/action | A szolgáltatás hoz létre egy kiszolgálópéldányt. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Beolvassa a riasztásokat a kiszolgálón. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Az api kiszolgálói regisztrációhoz a hitelesítő adatainak lekérése bevezetése új kiszolgálók neve. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Egy adott kiszolgálóhoz az API-t, amely a kiszolgálók és a legújabb idővel minden feltöltendő feltöltése adattípusokat listáját kéri le. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/delete | Törli a szolgáltatásban egy kiszolgálópéldányt. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Lekéri egy adott szinkronizálási szolgáltatása a szinkronizálási exportálása hiba részletes adatait. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Adja meg a szolgáltatás, az API-t lekéri a metrikák adatait.<br>Például az API segítségével kapcsolatos információk: extranetes a fiók zárolásának, a sikertelen kérelmek teljes, a szálankénti függőben lévő jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítések/mp, LDAP sikeres kötések/mp-ben, LDAP-kötés idő, aktív szálak LDAP, Kerberos-hitelesítések/mp stb ATQ szálak teljes ADDomain szolgáltatás.<br>Profil késés, a TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/read | Beolvassa a kiszolgálópéldány a szolgáltatásban. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Lekéri egy adott bérlő esetében a szolgáltatás konfigurációját. |
> | Műveletek | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Lekéri egy adott bérlő funkció engedélyezési állapota. |
> | Műveletek | Microsoft.ADHybridHealthService/services/write | A bérlő szolgáltatáspéldány hoz létre. |
> | Műveletek | Microsoft.ADHybridHealthService/unregister/action | Az előfizetés ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Advisor/configurations/read | Konfiguráció beolvasása |
> | Műveletek | Microsoft.Advisor/configurations/write | Frissítések konfigurálása |
> | Műveletek | Microsoft.Advisor/generateRecommendations/action | Létrehozza a javaslatokat |
> | Műveletek | Microsoft.Advisor/generateRecommendations/read | Lekérdezi a javaslatok állapota készítése |
> | Műveletek | Microsoft.Advisor/operations/read | A Microsoft Advisor műveletek beolvasása |
> | Műveletek | Microsoft.Advisor/recommendations/available/action | Új javaslat érhető el a Microsoft advisor |
> | Műveletek | Microsoft.Advisor/recommendations/read | Javaslatok beolvasása |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/delete | Mellőzés törlése |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/read | Suppressions beolvasása |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/write | Frissítések suppressions |
> | Műveletek | Microsoft.Advisor/register/action | A Microsoft Advisor előfizetés regisztrálása |
> | Műveletek | Microsoft.Advisor/suppressions/delete | Mellőzés törlése |
> | Műveletek | Microsoft.Advisor/suppressions/read | Suppressions beolvasása |
> | Műveletek | Microsoft.Advisor/suppressions/write | Frissítések suppressions |
> | Műveletek | Microsoft.Advisor/unregister/action | A Microsoft Advisor az előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AlertsManagement/alerts/changestate/action | A riasztás állapotának módosítása |
> | Műveletek | Microsoft.AlertsManagement/alerts/read | Minden riasztás beolvasása a bemeneti szűrőket. |
> | Műveletek | Microsoft.AlertsManagement/alertsSummary/read | Tartalmazza a riasztások összefoglaló lekérése |
> | Műveletek | Microsoft.AlertsManagement/Operations/read | A megadott műveletek beolvasása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/changestate/action | Az intelligens csoport állapotának módosítása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/read | A bemeneti szűrőket a intelligens csoportok lekérése |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes, és nincs használatban. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationresults/read | Lekéri a megadott műveleti eredményekre vonatkozó információkat. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationstatuses/read | A megadott művelet állapotára adatait kérdezi le. |
> | Műveletek | Microsoft.AnalysisServices/operations/read | Lekéri a műveletekre vonatkozó információkat |
> | Műveletek | Microsoft.AnalysisServices/register/action | Analysis Services-erőforrás-szolgáltató regisztrálása. |
> | Műveletek | Microsoft.AnalysisServices/servers/delete | Törli az Analysis Servert. |
> | Műveletek | Microsoft.AnalysisServices/servers/listGatewayStatus/action | A kiszolgálóval társított átjáró állapotának listázása. |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az Analysis Server diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az Analysis Server diagnosztikai beállításait |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Kiszolgálók elérhető naplók beolvasása |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Az Analysis Server elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.AnalysisServices/servers/read | Lekéri a megadott Analysis Serverre vonatkozó információkat. |
> | Műveletek | Microsoft.AnalysisServices/servers/resume/action | Az Analysis Server folytatása. |
> | Műveletek | Microsoft.AnalysisServices/servers/skus/read | A kiszolgáló Termékváltozatokra elérhető információinak beolvasása |
> | Műveletek | Microsoft.AnalysisServices/servers/suspend/action | Felfüggeszti az Analysis Servert. |
> | Műveletek | Microsoft.AnalysisServices/servers/write | Létrehozza vagy frissíti a megadott Analysis Servert. |
> | Műveletek | Microsoft.AnalysisServices/skus/read | Lekéri a termékváltozatokra vonatkozó információkat |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ApiManagement/checkNameAvailability/read | Ellenőrzi, hogy a megadott szolgáltatásnév érhető el |
> | Műveletek | Microsoft.ApiManagement/operations/read | Minden API-művelet Microsoft.ApiManagement erőforrás olvasása |
> | Műveletek | Microsoft.ApiManagement/register/action | Microsoft.ApiManagement erőforrás-szolgáltató előfizetésének regisztrálása |
> | Műveletek | Microsoft.ApiManagement/reports/read | Eltelt idő, földrajzi régió, a fejlesztők, termékek, API-k, üzemeltetési, előfizetés és byRequest összesített jelentések lekérése. |
> | Műveletek | Microsoft.ApiManagement/service/apis/delete | Távolítsa el a meglévő API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/delete | Távolítsa el a meglévő diagnosztika |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/read | Diagnosztikai vagy Get diagnosztikai adatait tartalmazó lista beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/write | Új diagnosztikai hozzáadása vagy meglévő diagnosztikai részletek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Eltávolítja a meglévő mellékletek |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/read | Probléma mellékletek vagy lekérdezi az API Management a probléma melléklet részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/write | Api-probléma melléklet hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/delete | Eltávolítja a meglévő megjegyzés |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/read | Lekérdezi ki megjegyzéseket vagy lekérdezi az API Management probléma Megjegyzés részletei |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/write | Api problémához fűzött megjegyzés hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/delete | Eltávolítja a meglévő probléma |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/read | API-t vagy az API Management lekérdezi a probléma részleteivel kapcsolatos problémák lekérése |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/write | API-t a probléma a hozzáadandó vagy frissítendő api probléma |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/delete | Távolítsa el a meglévő API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/delete | API-művelet szabályzatokból származó házirend-konfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/read | Szabályzatok lekérése az API-művelet vagy a Get-szabályzat konfigurációs adatainak API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/write | Állítsa be a szabályzat konfigurációs adatainak API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/delete | A művelet házirend-konfigurációt eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/read | Szabályzat konfigurációs adatainak beolvasása művelethez |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/write | Állítsa be a művelet házirend konfigurációs adatait |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/read | Meglévő API-műveletek listájának beolvasása, vagy szerezze be API-művelet részletei |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/delete | Meglévő címke, a művelet a társítás törlése |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/read | Címkék tartozó műveletet vagy az első címke részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/write | Meglévő címke társítása meglévő művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/write | Új API-művelet létrehozása vagy meglévő API-művelet frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apis/operationsByTags/read | Művelet/címke társítások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/delete | Távolítsa el a szabályzat konfigurációs API szabályzatokból származó |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/read | Szabályzatok lekérése az API-t vagy a Get szabályzat konfigurációs adatainak API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/write | Állítsa be a szabályzat konfigurációs adatainak API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/delete | Távolítsa el a szabályzat konfigurációs API-ból |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/read | Szabályzat konfigurációs adatainak lekérése az API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/write | Állítsa be a szabályzat konfigurációs adatainak API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/products/read | Minden termék, amely az API része beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/read | Az összes regisztrált API-k vagy a Get részletei az API-lista lekérése |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/delete | Eltávolítja az összes kiadása az API-t vagy API eltávolítása kiadás |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/read | Get-kiadások API reelase egy API-t vagy a Get részleteit |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/write | Új API-verziók létrehozása vagy meglévő API-kiadás frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/delete | Eltávolítja az összes, API-változat |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/read | API-khoz tartozó változatok beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/delete | Eltávolítja a meglévő séma |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/document/read | A séma ismertető dokumentumot beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/document/write | Frissítse a dokumentumot, amely leírja a séma |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/read | Összes séma beolvasása egy adott API számára, vagy lekérdezi az API által használt sémával |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/write | Beállítja az API által használt sémával |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Távolítsa el a címke leírását az API-ból |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Lépjen be címkéket, leírásokat hatóköre üres API-t vagy beolvasása a címke leírását az API hatóköre |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Hozzon létre/módosítsa a címke leírását hatókörében API |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/delete | Meglévő API/címke társításának megszüntetése |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/read | Minden API/címke társítás API/címke társítás API-t vagy a Get részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/write | Új API/címke-társítás hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/write | Új API-t létrehozni vagy frissíteni a meglévő API-részletek |
> | Műveletek | Microsoft.ApiManagement/service/apisByTags/read | API/címke társítások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/delete | Távolítsa el a meglévő VersionSet |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/read | Verzió-csoport entitásokat, vagy egy VersionSet részleteinek beolvasása listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/versions/read | Verzió Entitáslista beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/write | Hozzon létre új VersionSet, vagy meglévő VersionSet részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Frissíti a frissített hálózati beállítások választja ki a virtuális hálózatban futtatott Microsoft.ApiManagement erőforrásokat. |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/delete | Távolítsa el a meglévő engedélyezési kiszolgáló |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/read | Engedélyezési kiszolgálók listájának beolvasása, vagy szerezze be az engedélyezési kiszolgáló részletei |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/write | Hozzon létre egy új az engedélyezési kiszolgáló vagy egy meglévő engedélyezési kiszolgáló részletek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/backends/delete | Távolítsa el a meglévő háttérrendszerből |
> | Műveletek | Microsoft.ApiManagement/service/backends/read | Háttérrendszerek listájának beolvasása, vagy a háttérrendszer részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/backends/reconnect/action | Újrakapcsolódás kérelem létrehozása |
> | Műveletek | Microsoft.ApiManagement/service/backends/write | Adjon hozzá egy új háttér vagy a meglévő háttérbeli adatok frissítése |
> | Műveletek | Microsoft.ApiManagement/service/backup/action | Biztonsági mentési API Management szolgáltatás az adott tárolóba, a felhasználó a megadott tárfiók |
> | Műveletek | Microsoft.ApiManagement/service/certificates/delete | Meglévő tanúsítvány törlése |
> | Műveletek | Microsoft.ApiManagement/service/certificates/read | Tanúsítványok listájának lekérése vagy a tanúsítvány részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/certificates/write | Új tanúsítvány felvétele |
> | Műveletek | Microsoft.ApiManagement/service/delete | API Management szolgáltatás-példány törlése |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/delete | Távolítsa el a meglévő diagnosztika |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/read | Diagnosztikai vagy Get diagnosztikai adatait tartalmazó lista beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/write | Új diagnosztikai hozzáadása vagy meglévő diagnosztikai részletek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/getssotoken/action | Lekérdezi az SSO-jogkivonat, amely használható az API Management szolgáltatás örökölt portáljára rendszergazdaként a bejelentkezéshez |
> | Műveletek | Microsoft.ApiManagement/service/groups/delete | Meglévő csoport eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/groups/read | Csoportok vagy egy csoport részleteinek beolvasása listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/delete | Távolítsa el a meglévő felhasználó meglévő csoportból |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/read | Felhasználók listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/write | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Műveletek | Microsoft.ApiManagement/service/groups/write | Új csoport létrehozása vagy meglévő csoport részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/delete | Távolítsa el a meglévő identitásszolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/read | Identitás-szolgáltatóktól vagy Get részletek Identitásszolgáltatók listájának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/write | Hozzon létre egy új identitásszolgáltató vagy frissítés részletei meglévő Identitásszolgáltatók |
> | Műveletek | Microsoft.ApiManagement/service/locations/networkstatus/read | A helyen, amelyen a szolgáltatás függ erőforrások hálózati hozzáférési állapotát olvassa be. |
> | Műveletek | Microsoft.ApiManagement/service/loggers/delete | Távolítsa el a meglévő naplózót |
> | Műveletek | Microsoft.ApiManagement/service/loggers/read | Másolása listájának beolvasása, vagy naplózó részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/loggers/write | Új naplózó hozzáadása vagy meglévő naplózó részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/managedeployments/action | Módosítsa az SKU/egység, API Management szolgáltatás hozzáadása/eltávolítása regionálisan üzemelő példányokba |
> | Műveletek | Microsoft.ApiManagement/service/networkstatus/read | Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/action | Értesítést küld egy adott felhasználó |
> | Műveletek | Microsoft.ApiManagement/service/notifications/read | Lekéri az összes API Management közzétevő értesítések vagy lekérése az API Management közzétevő értesítés részletei |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Eltávolítja a meglévő társított egy értesítési e-mail cím |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Társított API Management közzétevő értesítési e-mailek címzettjeinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Új E-mail címzett az értesítés létrehozása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Az értesítés címzettjeinek társított felhasználó eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Az értesítés társított címzett felhasználók beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Az értesítés címzettjeinek felhasználó hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/write | Létrehozása vagy frissítése az API Management közzétevő értesítés |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/delete | Távolítsa el a meglévő OpenID Connect-szolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/read | OpenID Connect-szolgáltatókkal vagy az OpenID Connect-szolgáltató Get részleteit tartalmazó lista beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/write | Hozzon létre egy meglévő OpenID Connect-szolgáltató egy új OpenID Connect-szolgáltató vagy a frissítés részletei |
> | Műveletek | Microsoft.ApiManagement/service/operationresults/read | Aktuális hosszú ideje futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/policies/delete | Bérlői szabályzatokat szabályzatkonfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/policies/read | Házirendek bérlő bérlői vagy Get szabályzat konfigurációs adatainak lekérése |
> | Műveletek | Microsoft.ApiManagement/service/policies/write | Állítsa be a szabályzat konfigurációs adatainak bérlő |
> | Műveletek | Microsoft.ApiManagement/service/policySnippets/read | Az összes szabályzatkódrészlet beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/read | Regisztráció a beállítások lekérése a portálon vagy a Get jelentkezzen be a portál beállításait, vagy a portál a delegálási beállítások beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/write | Regisztrációs beállítások vagy frissítés regisztráció beállítások vagy frissítés bejelentkezési beállítások vagy frissítés bejelentkezési beállítások vagy frissítés delegálási beállítások vagy frissítés delegálási beállítások frissítése |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/delete | Távolítsa el a meglévő API-hoz a meglévő termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/read | Meglévő termékhez hozzáadott API-k listájának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/write | Meglévő API hozzáadása meglévő termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/delete | Távolítsa el a meglévő termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/delete | A meglévő termék meglévő fejlesztői csoport társítás törlése |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/read | A termék társított fejlesztői csoportok listájának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/write | Meglévő fejlesztői csoport társítása meglévő termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/delete | Távolítsa el a házirend-konfigurációt a termék házirendek |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/read | Házirendek termék termék vagy a Get szabályzat konfigurációs adatainak lekérése |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/write | Állítsa be a szabályzat konfigurációs adatait a termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/delete | Meglévő termékből házirend-konfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/read | A termék meglévő házirend-konfiguráció beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/write | Állítsa be a termék meglévő szabályzat beállítása |
> | Műveletek | Microsoft.ApiManagement/service/products/read | Termékek listájának beolvasása, vagy szerezze be a termék részletei |
> | Műveletek | Microsoft.ApiManagement/service/products/subscriptions/read | A termék előfizetések listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/delete | Meglévő címke meglévő termékkel társítás törlése |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/read | A termék vagy az első címke részleteinek társított címkék beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/write | Meglévő címke társítása meglévő termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/write | Új termék létrehozása vagy meglévő termékadatok frissítése |
> | Műveletek | Microsoft.ApiManagement/service/productsByTags/read | Termék/címke társítások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/properties/delete | Eltávolítja a meglévő tulajdonság |
> | Műveletek | Microsoft.ApiManagement/service/properties/read | Beolvassa az összes tulajdonság listáját, vagy a megadott tulajdonság részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/properties/write | Új tulajdonság létrehozza vagy frissíti a megadott tulajdonság értéke |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | API Management szolgáltatás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Az API Management szolgáltatás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | API Management szolgáltatás elérhető naplók beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | API Management szolgáltatás elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/read | Időszakra vonatkozó kvóta számlálóérték beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/write | Állítsa be a jelenlegi kvóta számlálóérték |
> | Műveletek | Microsoft.ApiManagement/service/quotas/read | Kvóta értékek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/quotas/write | Állítsa be a jelenlegi kvóta számlálóérték |
> | Műveletek | Microsoft.ApiManagement/service/read | Egy API Management szolgáltatáspéldány metaadatok olvasása |
> | Műveletek | Microsoft.ApiManagement/service/reports/read | Eltelt idő vagy a földrajzi régió vagy a fejlesztők által összesített Get jelentés szerint összesítve Get jelentés szerint összesítve jelentés beolvasása.<br>vagy termékek szerint összesítve jelentés beolvasása.<br>vagy a műveletek vagy előfizetés szerint összesítve Get jelentés szerint összesítve API-k vagy a Get jelentés szerint összesítve jelentés beolvasása.<br>vagy a jelentés adatainak-kérések lekérése |
> | Műveletek | Microsoft.ApiManagement/service/restore/action | A megadott tároló, a felhasználó által megadott tárfiókot az API Management szolgáltatás visszaállítása |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/delete | Előfizetés törlése. Ez a művelet segítségével való feliratkozás törlése |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/read | A termék előfizetések listáját, vagy szerezze be a termék-előfizetés részleteit |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Előfizetés elsődleges kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Előfizetés a másodlagos kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/write | Feliratkozás egy meglévő felhasználó meglévő termékhez vagy a meglévő előfizetés részletei. Ez a művelet használható előfizetés megújítása |
> | Műveletek | Microsoft.ApiManagement/service/tagResources/read | Társított erőforrásokkal rendelkező címkék listájának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/tags/delete | Meglévő címke eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/tags/read | Címkék listájának lekérése vagy címke részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tags/write | Új címke hozzáadása vagy meglévő címke részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/templates/delete | Alapértelmezett az API Management e-mail sablon alaphelyzetbe állítása |
> | Műveletek | Microsoft.ApiManagement/service/templates/read | Az összes e-mail-sablonok vagy lekérdezi az API Management e-mail sablon részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/templates/write | Létrehozni vagy frissíteni az API Management e-mail-sablon vagy frissíti az API Management e-mail-sablon |
> | Műveletek | Microsoft.ApiManagement/service/tenant/delete | A bérlői házirend-konfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/deploy/action | A alkalmazni a módosításokat a megadott git-ágak a konfigurációs adatbázis egy központi telepítési feladatot futtatja. |
> | Műveletek | Microsoft.ApiManagement/service/tenant/operationResults/read | Műveleti eredmények listája vagy egy adott művelet eredményének beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/read | Részletekért szabályzatkonfiguráció vagy a Get-bérlőhöz tartozó hozzáférési információk |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Az elsődleges elérési kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Másodlagos hozzáférési kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/save/action | Véglegesítés hoz létre a tárház megadott ágában konfiguráció pillanatkép |
> | Műveletek | Microsoft.ApiManagement/service/tenant/syncState/read | Git a legutóbbi szinkronizálás állapotának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/tenant/validate/action | Ellenőrzi a megadott git-ágak változtatásai |
> | Műveletek | Microsoft.ApiManagement/service/tenant/write | Állítsa be a bérlői vagy bérlői hozzáférési információk részletek frissítése szabályzat beállítása |
> | Műveletek | Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
> | Műveletek | Microsoft.ApiManagement/service/updatehostname/action | A telepítő, frissítenie vagy eltávolíthat egyéni tartományneveket egy API Management szolgáltatás |
> | Műveletek | Microsoft.ApiManagement/service/users/action | Új felhasználó regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/delete | A melléklet eltávolítására |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/read | Lekérdezi az alkalmazás a mellékletek vagy a melléklet beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/write | Az alkalmazás melléklet hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/delete | Eltávolítja a meglévő alkalmazás |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/read | Minden felhasználó az alkalmazások és az API Management lekérdezi az alkalmazás részleteinek listájának lekérése |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/write | Az API Management vagy a frissítések alkalmazás adatait az alkalmazás regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/users/delete | Felhasználói fiók eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Egyszeri bejelentkezési URL-címet generálni. Az URL-címet a felügyeleti portál eléréséhez használható |
> | Műveletek | Microsoft.ApiManagement/service/users/groups/read | Felhasználói csoportok listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/read | A regisztrált felhasználók listáját, vagy szerezze be a felhasználói fiók adatai |
> | Műveletek | Microsoft.ApiManagement/service/users/subscriptions/read | Felhasználói előfizetések listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/token/action | Egy felhasználó jogkivonat szükséges hozzáférési jogkivonat beszerzése |
> | Műveletek | Microsoft.ApiManagement/service/users/write | Regisztráljon egy új felhasználót vagy egy meglévő felhasználó fiók részletek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/write | API Management szolgáltatás új példányának létrehozása |
> | Műveletek | Microsoft.ApiManagement/unregister/action | Előfizetés regisztrációjának Microsoft.ApiManagement erőforrás-szolgáltató |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Authorization/checkAccess/action | Ellenőrzi, hogy a hívó jogosult-e egy adott művelet végrehajtására |
> | Műveletek | Microsoft.Authorization/classicAdministrators/delete | Az előfizetés rendszergazdájának eltávolítása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/read | Az előfizetés rendszergazdáinak beolvasása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/write | Az előfizetés rendszergazdájának beállítása vagy módosítása. |
> | Műveletek | Microsoft.Authorization/elevateAccess/action | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |
> | Műveletek | Microsoft.Authorization/locks/delete | A megadott hatókör zárolásainak törlése. |
> | Műveletek | Microsoft.Authorization/locks/read | A megadott hatókör zárolásainak beolvasása. |
> | Műveletek | Microsoft.Authorization/locks/write | A megadott hatókör zárolásainak beállítása. |
> | Műveletek | Microsoft.Authorization/operations/read | A műveletek listájának beolvasása |
> | Műveletek | Microsoft.Authorization/permissions/read | A hívó adott hatókörben érvényes engedélyeinek listázása. |
> | Műveletek | Microsoft.Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/policyAssignments/read | Szabályzat-hozzárendelés adatainak lekérése. |
> | Műveletek | Microsoft.Authorization/policyAssignments/write | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/delete | Szabályzat-definíció törlése. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/read | Szabályzat-definíció adatainak lekérése. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/write | Egyéni szabályzat-definíció létrehozása. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/delete | Szabályzatkészlet-definíció törlése. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/read | Egy szabályzatkészlet-definíció adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/write | Egyéni szabályzatkészlet-definíció létrehozása. |
> | Műveletek | Microsoft.Authorization/providerOperations/read | Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása. |
> | Műveletek | Microsoft.Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörből. |
> | Műveletek | Microsoft.Authorization/roleAssignments/read | Információk beolvasása a szerepkör-hozzárendelésről. |
> | Műveletek | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/read | Szerepkör-definíció adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/write | Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Olvassa el a regisztrációs adatokat egy Azure Automation DSC |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Írja a kérést az Azure Automation DSC-kulcsok újragenerálása |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/delete | Egy Azure Automation szolgáltatásbeli tanúsítványeszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/getCount/action | Beolvassa a tanúsítványok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/read | Lekérdezi egy Azure Automation szolgáltatásbeli tanúsítványeszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/write | Létrehoz vagy frissít egy Azure Automation szolgáltatásbeli tanúsítványeszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation a DSC-fordítási beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation a DSC-fordítási beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation a DSC-fordítási ír |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation a DSC-fordítási ír |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/content/read | A konfigurációs médiatartalmak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/delete | Egy Azure Automation DSC-tartalom törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/getCount/action | A szám egy Azure Automation DSC tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/read | Egy Azure Automation DSC-tartalom beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/write | Egy Azure Automation DSC tartalmat ír |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation szolgáltatásbeli kapcsolódási eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/getCount/action | Beolvassa a kapcsolatok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/read | Azure Automation szolgáltatásbeli kapcsolódási eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/write | Létrehoz vagy frissít egy Azure Automation szolgáltatásbeli kapcsolódási eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/read | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/write | Létrehoz egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation szolgáltatásbeli hitelesítőadat-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/getCount/action | Beolvassa a hitelesítő adatok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation szolgáltatásbeli hitelesítőadat-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli hitelesítőadat-eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/delete | Azure Automation-fiók törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid forgatókönyv-feldolgozó erőforrások törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid forgatókönyv-feldolgozó erőforrások beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/output/read | A feladat kimenetének beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Az Azure Automation-runbook tartalmának beolvasása a feladat végrehajtásának időpontjában |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation-feladat létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation-feladatütemezés törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation-feladatütemezés beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation-feladatütemezés |
> | Műveletek | Microsoft.Automation/automationAccounts/linkedWorkspace/read | A munkaterület társítva, az automation-fiók beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/listKeys/action | Az automation-fiók kulcsok beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/logDefinitions/read | Az automation-fiókhoz elérhető naplók beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/activities/read | Lekérdezi az Azure Automation-tevékenységek |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation-modul törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/getCount/action | Lekérdezi a modulokat az Automation-fiókon belül száma |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/read | Azure Automation-modul beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/write | Létrehoz vagy frissít egy Azure Automation-modul |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Egy Azure Automation DSC csomópont-konfiguráció törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Beolvassa egy Azure Automation DSC-csomópont konfigurációjának tartalma |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Egy Azure Automation DSC-csomópont-konfiguráció beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Egy Azure Automation DSC-csomópont-konfiguráció írása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodecounts/read | Csomópontok száma összefoglaló beolvassa a megadott típus |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/delete | Törli az Azure Automation DSC-csomópontok |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/read | Olvassa be az Azure Automation DSC-csomópontok |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC-jelentések beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/write | Létrehozza vagy frissíti az Azure Automation DSC-csomópontok |
> | Műveletek | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Az Azure Automation TypeFields beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Automation Metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/read | Azure Automation-fiók beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/content/read | Azure Automation-runbook tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation-runbook törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Azure Automation-runbookvázlat tartalmának |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation-runbook draft művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation-runbookvázlat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Egy Azure Automation runbookvázlat-tesztelési feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Egy Azure Automation runbookvázlat-tesztelési feladat folytatása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Egy Azure Automation runbookvázlat-tesztelési feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Egy Azure Automation runbookvázlat-tesztelési feladat felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Létrehoz egy Azure Automation runbookvázlat-tesztelési feladat |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Azure Automation-runbookvázlat szerkesztési műveleteinek visszavonása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Lekérdezi az Azure Automation-runbookok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation-runbookvázlat közzététele |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation-runbook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/write | Létrehoz vagy frissít egy Azure Automation-runbook |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/delete | Egy Azure Automation szolgáltatásbeli ütemezési eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/getCount/action | Lekérdezi az Azure Automation ütemezések száma |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/read | Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/write | Létrehoz vagy frissít egy Azure Automation szolgáltatásbeli ütemezési eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Egy Azure Automation szoftverfrissítési konfiguráció törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Egy Azure Automation szoftverfrissítési konfiguráció beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Létrehozza vagy frissíti az Azure Automation szoftverfrissítési konfiguráció |
> | Műveletek | Microsoft.Automation/automationAccounts/statistics/read | Az Azure Automation statisztika lekérése |
> | Műveletek | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Egy Azure Automation frissítés üzembe helyezési gép beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Egy Azure Automation update management javítási feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/usages/read | Az Azure Automation használatának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation-változóeszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/read | Azure Automation-változóeszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/write | Létrehoz vagy frissít egy Azure Automation-változóeszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation-figyelő feladat törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/read | Egy Azure Automation-figyelő feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation-figyelő feladat indítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation-figyelő feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/streams/read | Egy Azure Automation megfigyelő-feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Egy Azure Automation-figyelő feladat műveletek törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Egy Azure Automation-figyelő feladat műveletek beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Hozzon létre egy Azure Automation-figyelő feladat műveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/write | Létrehoz egy Azure Automation-figyelő feladat |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/action | Azure Automation-webhook URI generálása |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation-webhook törlése  |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/read | Azure Automation-webhook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/write | Létrehoz vagy frissít egy Azure Automation-webhook |
> | Műveletek | Microsoft.Automation/automationAccounts/write | Egy Azure Automation-fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft.Automation/operations/read | Azure Automation-erőforrások számára elérhető műveletek beolvasása |
> | Műveletek | Microsoft.Automation/register/action | Regisztrálja az előfizetést az Azure Automationhöz |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C-címtárerőforrás törlése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C-címtárerőforrás megtekintése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-címtárerőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft.AzureActiveDirectory/operations/read | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz választható összes API-művelet olvasása |
> | Műveletek | Microsoft.AzureActiveDirectory/register/action | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureStack/Operations/read | Egy erőforrás-szolgáltató művelet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/register/action | Az előfizetés regisztrálása a Microsoft.AzureStack erőforrás-szolgáltató |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Egy ügyfél az Azure Stack-előfizetés törlése |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/read | Az Azure Stack ügyfél-előfizetés tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/write | Létrehozza vagy frissíti az Azure Stack ügyfél-előfizetés |
> | Műveletek | Microsoft.AzureStack/registrations/delete | Az Azure Stack-regisztráció törlése |
> | Műveletek | Microsoft.AzureStack/registrations/getActivationKey/action | A legújabb Azure Stackkel használható aktiválási kulcs beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/products/listDetails/action | Kiterjesztett az Azure Stack piactéren termék részleteinek beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/products/read | Az Azure Stack piactéren termék tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/read | Az Azure Stack-regisztrációk tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/write | Létrehozza vagy frissíti az Azure Stack-regisztráció |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/delete | Alkalmazás törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/read | Alkalmazás fel van sorolva, vagy egy alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Alkalmazáscsomag aktiválása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/delete | Alkalmazáscsomag törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/read | Alkalmazáscsomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/write | Egy új alkalmazás-csomag létrehozása vagy meglévő alkalmazáscsomag frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/write | Létrehoz egy új alkalmazást, vagy egy meglévő alkalmazás frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/certificateOperationResults/read | A Batch-fiók egy hosszú ideig futó tanúsítvány művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | A tanúsítvány a Batch-fiók törlésére tett sikertelen kísérlet megszakítása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/delete | Töröl egy tanúsítványt egy Batch-fiókból |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/read | Megjeleníti a Batch-fiók tanúsítványokat vagy a tanúsítvány tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/write | Létrehoz egy új tanúsítványt a Batch-fiók, vagy egy meglévő tanúsítvány frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/delete | Batch-fiók törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/listkeys/action | Listák hozzáférési kulcsok egy Batch-fiókhoz |
> | Műveletek | Microsoft.Batch/batchAccounts/operationResults/read | Egy Batch-fiók hosszú ideje futó művelet eredményének beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/poolOperationResults/read | A Batch-fiók egy hosszú ideig futó készlet művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/delete | Törli egy készletet a Batch-fiók |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Letiltja az automatikus méretezés a Batch-fiók készlet |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/read | Felsorolja a készletek a Batch-fiók vagy a készlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/stopResize/action | Leállítja egy folyamatban lévő műveletet a Batch-fiók készlet átméretezése |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Frissíti az operációs rendszert, a Batch-fiók készlet |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/write | Létrehoz egy új készletet a Batch-fiók, vagy egy meglévő készlet frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | A Batch szolgáltatás elérhető naplók beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Batch szolgáltatás elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Batch/batchAccounts/read | Batch-fiókok listája vagy egy Batch-fiók tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/regeneratekeys/action | Újragenerálja a hozzáférési kulcsokat a Batch-fiók |
> | Műveletek | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Egy Batch-fiókhoz konfigurált automatikus tárfiók hozzáférési kulcsait szinkronizálja |
> | Műveletek | Microsoft.Batch/batchAccounts/write | Létrehoz egy új Batch-fiókot, vagy egy meglévő Batch-fiók frissítése |
> | Műveletek | Microsoft.Batch/locations/checkNameAvailability/action | Ellenőrzi, hogy a fiók neve érvényes, és nincs használatban. |
> | Műveletek | Microsoft.Batch/locations/quotas/read | A Bach kvótái a megadott előfizetés lekérdezi a megadott Azure-régiónál |
> | Műveletek | Microsoft.Batch/operations/read | Microsoft.Batch erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.Batch/register/action | A Batch erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi, hogy a Batch-fiókok létrehozása |
> | Műveletek | Microsoft.Batch/unregister/action | A Batch erőforrás-szolgáltató megakadályozza, hogy a Batch-fiókok létrehozása az előfizetés regisztrációjának törlése |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BatchAI/clusters/read | Batch AI-fürtök listája vagy egy Batch AI-fürtön tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/fileservers/read | Batch AI fileservers sorolja fel, vagy egy Batch AI-fájlkiszolgáló tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/locations/operationresults/read | A megadott Azure-régiónál Batch AI az aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft.BatchAI/locations/operationstatuses/read | A megadott Azure-régiónál Batch AI az aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft.BatchAI/locations/usages/read | A megadott előfizetés Batch AI lebontott lekérdezi a megadott Azure-régiónál |
> | Műveletek | Microsoft.BatchAI/register/action | A Batch AI erőforrás-szolgáltató előfizetésének regisztrálása és a Batch AI-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.BatchAI/unregister/action | A Batch AI erőforrás-szolgáltató megakadályozza, hogy a Batch AI-erőforrások létrehozása az előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.BatchAI/workspaces/clusters/delete | A Batch AI-fürt törlése |
> | Műveletek | Microsoft.BatchAI/workspaces/clusters/read | Batch AI-fürtök listája vagy egy Batch AI-fürtön tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/workspaces/clusters/remoteLoginInformation/action | Egy Batch AI-fürt távoli bejelentkezési adatokat listázza |
> | Műveletek | Microsoft.BatchAI/workspaces/clusters/write | Létrehoz egy új Batch AI-fürtöt, vagy egy meglévő Batch AI-fürt frissítése |
> | Műveletek | Microsoft.BatchAI/workspaces/delete | Egy Batch AI-munkaterület törlése |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/delete | Egy Batch AI kísérletet törlése |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/delete | Egy Batch AI-feladat törlése |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/listoutputfiles/action | A kimeneti fájlokat egy Batch AI-feladat listája |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/read | Batch AI-feladatok listája vagy egy Batch AI-feladat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/remoteLoginInformation/action | Egy Batch AI-feladat távoli bejelentkezési adatokat listázza |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/terminate/action | Egy Batch AI-feladat leáll |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/jobs/write | Egy új Batch AI-feladat létrehozása vagy frissítése egy meglévő Batch AI-feladat |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/read | Batch AI listák kísérleteket, vagy egy Batch AI kísérletet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/workspaces/experiments/write | Batch AI új kísérlet létrehozása vagy frissítése egy már meglévő Batch AI kísérlet |
> | Műveletek | Microsoft.BatchAI/workspaces/fileservers/delete | Törli a Batch AI-fájlkiszolgáló |
> | Műveletek | Microsoft.BatchAI/workspaces/fileservers/read | Batch AI fileservers sorolja fel, vagy egy Batch AI-fájlkiszolgáló tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/workspaces/fileservers/write | Egy új Batch AI-fájlkiszolgáló létrehozása vagy frissítése egy már meglévő Batch AI-fájlkiszolgáló |
> | Műveletek | Microsoft.BatchAI/workspaces/read | Batch AI-munkaterületek listája vagy egy Batch AI-munkaterület tulajdonságainak beolvasása |
> | Műveletek | Microsoft.BatchAI/workspaces/write | Batch AI új munkaterület létrehozása vagy frissítése egy meglévő Batch AI-munkaterületet |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Billing/billingPeriods/read | Felsorolja a rendelkezésre álló elszámolási időszakok |
> | Műveletek | Microsoft.Billing/invoices/read | Listák elérhető számlák |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BingMaps/mapApis/Delete | Törlési művelet |
> | Műveletek | Microsoft.BingMaps/mapApis/listSecrets/action | A titkos kulcsok listázása |
> | Műveletek | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása |
> | Műveletek | Microsoft.BingMaps/mapApis/Read | Olvasási művelet |
> | Műveletek | Microsoft.BingMaps/mapApis/regenerateKey/action | A kulcs újragenerálása |
> | Műveletek | Microsoft.BingMaps/mapApis/Write | Írási művelet |
> | Műveletek | Microsoft.BingMaps/Operations/read | A művelet leírását. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BotService/botServices/channels/delete | A Bot Service törlése |
> | Műveletek | Microsoft.BotService/botServices/channels/read | Olvassa el a Bot Service |
> | Műveletek | Microsoft.BotService/botServices/channels/write | A Bot Service írása |
> | Műveletek | Microsoft.BotService/botServices/connections/delete | A Bot Service törlése |
> | Műveletek | Microsoft.BotService/botServices/connections/read | Olvassa el a Bot Service |
> | Műveletek | Microsoft.BotService/botServices/connections/write | A Bot Service írása |
> | Műveletek | Microsoft.BotService/botServices/delete | A Bot Service törlése |
> | Műveletek | Microsoft.BotService/botServices/read | Olvassa el a Bot Service |
> | Műveletek | Microsoft.BotService/botServices/write | A Bot Service írása |
> | Műveletek | Microsoft.BotService/locations/operationresults/read | Egy aszinkron művelet állapotának olvasása |
> | Műveletek | Microsoft.BotService/Operations/read | Minden erőforrástípus esetén a műveletek beolvasása |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Cache/checknameavailability/action | Annak ellenőrzése, hogy a név rendelkezésre áll-e egy új Redis Cache-hez való használatra |
> | Műveletek | Microsoft.Cache/locations/operationresults/read | Egy olyan hosszú ideje futó művelet eredményének beolvasása, amelynek esetében a Location (Hely) fejléc vissza lett küldve az ügyfélnek |
> | Műveletek | Microsoft.Cache/operations/read | A Microsoft.Cache szolgáltató által támogatott műveletek listázása. |
> | Műveletek | Microsoft.Cache/redis/delete | Teljes Redis gyorsítótár törlése |
> | Műveletek | Microsoft.Cache/redis/export/action | Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban |
> | Műveletek | Microsoft.Cache/redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabályainak törlése |
> | Műveletek | Microsoft.Cache/redis/firewallRules/read | Egy Redis Cache IP-tűzfalszabályainak beolvasása |
> | Műveletek | Microsoft.Cache/redis/firewallRules/write | Egy Redis Cache IP-tűzfalszabályainak szerkesztése |
> | Műveletek | Microsoft.Cache/redis/forceReboot/action | Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat. |
> | Műveletek | Microsoft.Cache/redis/import/action | Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból |
> | Műveletek | Microsoft.Cache/redis/linkedservers/delete | Egy Redis gyorsítótárhoz csatolt kiszolgáló törlése |
> | Műveletek | Microsoft.Cache/redis/linkedservers/read | Egy Redis gyorsítótárhoz hozzárendelt csatolt kiszolgálók beolvasása. |
> | Műveletek | Microsoft.Cache/redis/linkedservers/write | Csatolt kiszolgáló hozzáadása egy Redis gyorsítótárhoz |
> | Műveletek | Microsoft.Cache/redis/listKeys/action | Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/listUpgradeNotifications/read | A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása. |
> | Műveletek | Microsoft.Cache/redis/metricDefinitions/read | Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/delete | Egy Redis Cache javítási ütemezésének törlése |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/read | Egy Redis Cache javítási ütemezésének beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/write | Egy Redis Cache javítási ütemezésének módosítása |
> | Műveletek | Microsoft.Cache/redis/read | Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/regenerateKey/action | Redis gyorsítótár elérésikulcs-értékeinek módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/start/action | Gyorsítótárpéldány elindítása. |
> | Műveletek | Microsoft.Cache/redis/stop/action | Gyorsítótárpéldány leállítása. |
> | Műveletek | Microsoft.Cache/redis/write | Redis gyorsítótár beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/register/action | A Microsoft.Cache erőforrás-szolgáltató regisztrálása egy előfizetéshez |
> | Műveletek | Microsoft.Cache/unregister/action | A Microsoft.Cache erőforrás-szolgáltató egy előfizetéshez tartozó regisztrációjának törlése |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Capacity/register/action | A kapacitás erőforrás-szolgáltató regisztrálása, és lehetővé teszi a kapacitás-erőforrások létrehozását. |
> | Műveletek | Microsoft.Capacity/reservationorders/action | Bármely foglalási frissítése |
> | Műveletek | Microsoft.Capacity/reservationorders/delete | Bármely-foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/read | Minden foglalást olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/action | Bármely foglalási frissítése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/delete | Bármely-foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/read | Minden foglalást olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/revisions/read | Minden foglalást olvasása |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/write | Bármely-foglalás létrehozása |
> | Műveletek | Microsoft.Capacity/reservationorders/write | Bármely-foglalás létrehozása |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Műveletek | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/edgenodes/delete |  |
> | Műveletek | Microsoft.Cdn/edgenodes/read |  |
> | Műveletek | Microsoft.Cdn/edgenodes/write |  |
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
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításait |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az erőforrás diagnosztikai beállításait |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Microsoft.Cdn naplók beolvasása. |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/write |  |
> | Műveletek | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Műveletek | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Műveletek | Microsoft.Cdn/profiles/read |  |
> | Műveletek | Microsoft.Cdn/profiles/write |  |
> | Műveletek | Microsoft.Cdn/register/action | Az előfizetés regisztrálása a CDN erőforrás-szolgáltatónál, és a CDN-profilok létrehozásának engedélyezése. |
> | Műveletek | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Egy meglévő tanúsítvány törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Tanúsítványok listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adjon hozzá egy új tanúsítványt, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Delete | Egy meglévő appservice-tanúsítvány törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Read | Tanúsítványrendelések listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Tanúsítványrendelés újbóli kiadása egy meglévő |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Egy meglévő tanúsítványrendelés megújítása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Tanúsítvány e-mail újraküldése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Egy másik e-mail címet, a kérelem e-mailek újraküldése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Egy kibocsátott App Service-tanúsítvány a webhely lezárása beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Tanúsítvány-műveletek listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Tanúsítvány e-mailek előzményeinek beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Tartomány tulajdonjogának ellenőrzése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Write | Adjon hozzá egy új Tanúsítványrendelési vagy egy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/operations/Read | Az app service-tanúsítvány regisztrálása minden művelet listázása |
> | Műveletek | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Alkalmazás egyszerű szolgáltatás egyszerű szolgáltatás üzembe helyezése |
> | Műveletek | Microsoft.CertificateRegistration/register/action | Az előfizetés a Microsoft Certificates erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Tanúsítvány beszerzési objektum ellenőrzése nélkül elküldené azokat |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicCompute/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Az adott tartománynév elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Egy adott tartománynév elérhetőségének beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/active/write | Az aktív tartománynév beállítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/capabilities/read | A tartománynév-képességek megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/domainNames/delete | Erőforrások tartománynevének eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Üzembe helyezési pontok megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Az üzembe helyezési pont állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adja hozzá az üzembe helyezési pont állapotának. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/delete | A tartománynév-kiterjesztések eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/read | A tartománynév-kiterjesztéseket adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/write | Tartománynév-kiterjesztések hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Új belső terheléselosztás eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | A tartománynevek belső terheléselosztói műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | A belső terheléselosztók beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Új belső terheléselosztás létrehozása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Az elosztott terhelésű végpontcsoportok beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Az elosztott terhelésű végpontcsoport hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/operationstatuses/read | A tartománynév műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/read | Erőforrások tartománynevének visszaadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Az alkalmazott szolgáltatási tanúsítványok törlése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | A tartománynevek szolgáltatási tanúsítványai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Az alkalmazott szolgáltatási tanúsítványok visszaadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Alkalmazott szolgáltatási tanúsítványok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Üzembe helyezési pont migrálásának megszakítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Üzembe helyezési pont migrálásának véglegesítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/delete | A megadott üzembe helyezési pont törlése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | A tartománynevek üzembe helyezési pontjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Üzembe helyezési pont migrálásának előkészítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/read | Üzembe helyezési pontok megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Eltávolítja az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozásának hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | A tartománynév szerepköre metrikadefiníciójának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | A tartománynév szerepkörmetrikájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | A tartománynevek üzembe helyezési ponti szerepköre műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/read | Üzembe helyezési pont szerepkörének beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | A tartománynév üzembe helyezési ponti szerepköre szerepkörpéldánya távoli asztali kapcsolatfájljának letöltése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepköre szerepkörpéldánya műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Szerepkörpéldány beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | A szerepkörpéldány újraépítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Szerepkörpéldány rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | A szerepkörpéldányok újraindítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Az üzembe helyezési pont szerepköre termékváltozatának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/write | Az üzembe helyezési pont szerepkörének hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/start/action | Üzembe helyezési pont indítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Az üzembe helyezési pont állapotának leállítottra állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Az üzembe helyezési pont állapotának elindítottra állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/stop/action | Üzembe helyezési pont felfüggesztése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Verzióváltási tartomány feldolgozása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Üzembe helyezési pont migrálásának ellenőrzése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/write | Telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/swap/action | Előkészítési pont cseréje üzemelési pontra. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/write | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/moveSubscriptionResources/action | Minden hagyományos erőforrás áthelyezése egy másik előfizetésbe. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystemFamilies/read | Ez a művelet a Microsoft Azure-ban elérhető vendég operációsrendszer-családokat, valamint az egyes családokon belül elérhető operációsrendszer-verziókat listázza. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystems/read | Ez a művelet a Microsoft Azure-ban jelenleg elérhető vendég operációsrendszer-verziókat listázza. |
> | Műveletek | Microsoft.ClassicCompute/operations/read | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/register/action | Regisztrálás a Classic Compute szolgáltatónál |
> | Műveletek | Microsoft.ClassicCompute/resourceTypes/skus/read | Lekérdezi a támogatott erőforrástípusok termékváltozat-listáját. |
> | Műveletek | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Annak ellenőrzése, hogy áthelyezhető-e az előfizetés hagyományos áthelyezéssel. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | A virtuális géphez társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | A virtuális géphez társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Lehetséges aszinkron műveletek beolvasása |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Adatlemez csatlakoztatása a virtuális géphez. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/capture/action | Virtuális gép rögzítése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/delete | Virtuális gépek eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Adatlemez leválasztása a virtuális gépről. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Virtuális gép diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/disks/read | Adatlemezek listájának beolvasása |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Virtuális gép RDP-fájljának letöltése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/read | Virtuálisgép-bővítmény beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/write | Virtuálisgép-bővítmény hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | A virtuális gép metrikadefiníciójának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/metrics/read | Metrika beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati interfészhez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | A hálózati interfészhez társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | A virtuális gépek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | A művelet elvégzi a virtuális gép karbantartását. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/read | Virtuális gépek listájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/redeploy/action | A virtuális gép ismételt üzembe helyezése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/restart/action | Virtuális gépek újraindítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Virtuális gép rendszerének leállítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/start/action | Virtuális gép elindítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/stop/action | Virtuális gép leállítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Egy Express Route-keresztkapcsolat műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Express Route-keresztkapcsolati társviszony törlése. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Egy Express Route-keresztkapcsolati társviszony műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Express Route-keresztkapcsolati társviszony beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Express Route-keresztkapcsolati társviszony hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Az Express Route-keresztkapcsolatok beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Express Route-keresztkapcsolatok hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Támogatott eszközök listájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése. Ezt a műveletet az adatelemzési erőforrás-szolgáltató egészíti ki adatokkal. |
> | Műveletek | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | A hálózati biztonsági csoport lekérése eseményeinek beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/read | A hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | A biztonsági szabály beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Egy biztonsági szabály hozzáadása vagy frissítése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/write | Új hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/operations/read | A klasszikus hálózati műveletek beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/register/action | Regisztrálás a Classic Network szolgáltatónál |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/join/action | Csatlakozás egy fenntartott IP-címhez |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/write | Új fenntartott IP-cím felvétele |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Egy virtuális hálózat migrálásának megszakítása |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Az adott IP-cím virtuális hálózaton belüli elérhetőségét ellenőrzi. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Egy virtuális hálózat migrálásának véglegesítése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/delete | A virtuális hálózat törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Az ügyféltanúsítvány visszavonásának megszüntetése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Visszavont ügyféltanúsítványok beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Ügyféltanúsítvány visszavonása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | A virtuális hálózati átjáró ügyféltanúsítványának törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Tanúsítvány letöltése ujjlenyomat útján. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | A virtuális hálózati átjáró tanúsítványcsomagjának listázása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Ügyfél főtanúsítványainak megkeresése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Ügyfél új főtanúsítványának feltöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Webhelyek közötti átjárókapcsolat létesítése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Webhelyek közötti átjárókapcsolat leválasztása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Webhelyek közötti átjárókapcsolat tesztelése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Virtuális hálózati átjáró törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Eszközkonfigurációs parancsfájl letöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Átjáródiagnosztika letöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Kör szolgáltatáskulcsának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | A virtuális hálózati átjárócsomag listázása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | A virtuális hálózati átjárócsomag beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Virtuális hálózati átjárók beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának indítása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának leállítása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Virtuális hálózati átjáró hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Társviszony-létesítés két virtuális hálózat között. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Egy virtuális hálózat migrálásának előkészítése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | A távoli virtuális hálózati társviszony proxyjának törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | A távoli virtuális hálózati társviszony proxyjának beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | A távoli virtuális hálózati társviszony proxyjának hozzáadása vagy frissítése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális hálózat alhálózatához társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Az alhálózathoz társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Az alhálózathoz társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Egy virtuális hálózat migrálásának ellenőrzése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | A virtuális hálózati társviszony beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicStorage/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | A tárfiók elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Egy tárfiók elérhetőségének beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/disks/read | A tárfióklemez beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/images/operationstatuses/read | Lemezkép műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/images/read | A lemezkép visszaadása. |
> | Műveletek | Microsoft.ClassicStorage/operations/read | Klasszikus tárolási műveletek beolvasása |
> | Műveletek | Microsoft.ClassicStorage/osImages/read | A operációs rendszerkép beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/osPlatformImages/read | Az operációs rendszer platformlemezképének beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/publicImages/read | A nyilvános virtuális gép lemezképének beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/register/action | Regisztrálás egy hagyományos tárolóba. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Tárfiók migrálásának megszakítása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Tárfiók migrálásának végrehajtása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/delete | Tárfiók törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/delete | A megadott tárfióklemez törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/write | Új tárfióklemez felvétele. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/delete | A megadott tárfióklemezkép törlése. (Elavult. Használja a "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | A tárfióklemezkép műveleti állapotát adja vissza. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép adja vissza. (Elavult. Használja a "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/delete | A megadott tárfiók operációsrendszer-lemezképének törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/read | A tárfiók operációsrendszer-lemezképének visszaadása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/write | Egy adott tárfiók operációsrendszer-lemezképének hozzáadása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Tárfiók migrálásának előkészítése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Metrika beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/read | Elérhető szolgáltatások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Tárfiók migrálásának ellenőrzése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | A megadott virtuálisgép-lemezkép törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Egy adott virtuálisgép-lemezkép műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/read | A virtuálisgép-lemezkép beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/vmImages/write | A megadott virtuálisgép-lemezkép hozzáadása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/write | Új tárfiók hozzáadása. |
> | Műveletek | Microsoft.ClassicStorage/vmImages/read | A virtuálisgép-lemezképek listázása. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CognitiveServices/accounts/delete | API-fiókok törlése |
> | Műveletek | Microsoft.CognitiveServices/accounts/listKeys/action | Kulcsok listázása |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/logDefinitions/read | A Cognitive Services-fiókhoz elérhető naplók beolvasása |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Cognitive Serviceshez elérhető metrikák beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/read | API-fiókok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/regenerateKey/action | Kulcs újragenerálása |
> | Műveletek | Microsoft.CognitiveServices/accounts/skus/read | Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/usages/read | Egy meglévő erőforrás kvótahasználatának beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/write | API-fiókok írása. |
> | Műveletek | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Egy előfizetéshez elérhető termékváltozatok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/Operations/read | Az összes elérhető művelet listázása |
> | Műveletek | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |
> | Műveletek | Microsoft.CognitiveServices/skus/read | A rendelkezésre álló termékváltozatok beolvasása a Cognitive Services számára. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Commerce/RateCard/read | Értéket ad vissza adatokat, az erőforrás vagy mérőszám metaadatok és a díjakat az adott előfizetésben kínálnak. |
> | Műveletek | Microsoft.Commerce/UsageAggregates/read | A Microsoft Azure consumption lekéri egy előfizetés keretében. Az eredmény tartalmazza az összesítések a kapcsolódó információk, egy adott időtartományt a használati adatok, az előfizetésben és erőforráscsoportban. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Compute/availabilitySets/delete | Törli a rendelkezésre állási csoportot |
> | Műveletek | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/availabilitySets/vmSizes/read | A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása |
> | Műveletek | Microsoft.Compute/availabilitySets/write | Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot |
> | Műveletek | Microsoft.Compute/disks/beginGetAccess/action | Lemez SAS URI-jának beolvasása blobhozzáféréshez |
> | Műveletek | Microsoft.Compute/disks/delete | A lemez törlése |
> | Műveletek | Microsoft.Compute/disks/endGetAccess/action | A lemez SAS URI-jának visszavonása |
> | Műveletek | Microsoft.Compute/disks/read | A lemez tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/disks/write | Új lemez létrehozása vagy meglévő lemez frissítése |
> | Műveletek | Microsoft.Compute/galleries/delete | A katalógus törlése |
> | Műveletek | Microsoft.Compute/galleries/images/delete | A katalógus-lemezkép törlése |
> | Műveletek | Microsoft.Compute/galleries/images/read | Katalógus-lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/galleries/images/versions/delete | A katalóguslemezkép-verzió törlése |
> | Műveletek | Microsoft.Compute/galleries/images/versions/read | Katalóguslemezkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/galleries/images/versions/write | Új katalóguslemezkép-verzió létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/galleries/images/write | Új katalógus-lemezkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/galleries/read | Katalógus tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/galleries/write | Új katalógus létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/images/delete | A lemezkép törlése |
> | Műveletek | Microsoft.Compute/images/read | A lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/images/write | Új lemezkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/locations/capsOperations/read | Aszinkron Caps művelet állapotának beolvasása |
> | Műveletek | Microsoft.Compute/locations/diskOperations/read | Lemez aszinkron művelet állapotát olvassa be |
> | Műveletek | Microsoft.Compute/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Platformlemezképet tartalmazó ajánlat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Platformlemezképet tartalmazó termékváltozat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Platformlemezkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VM-bővítmény-típus tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VM-bővítmény-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/read | Gyártó tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/runCommands/read | A helyen elérhető futtatási parancsok listázása |
> | Műveletek | Microsoft.Compute/locations/usages/read | Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be |
> | Műveletek | Microsoft.Compute/locations/vmSizes/read | Listázza az adott helyen elérhető virtuálisgép-méreteket |
> | Műveletek | Microsoft.Compute/operations/read | A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.Compute/register/action | Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.Compute/restorePointCollections/delete | A visszaállításipont-gyűjtemény és az abban található visszaállítási pontok törlése |
> | Műveletek | Microsoft.Compute/restorePointCollections/read | Egy visszaállításipont-gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/read | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI azonosítóival |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/write | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Compute/restorePointCollections/write | Egy új visszaállításipont-gyűjtemény létrehozása, vagy egy meglévő módosítása |
> | Műveletek | Microsoft.Compute/sharedVMImages/delete | A megosztott VM-lemezkép törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/read | Megosztott VM-lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/delete | A megosztott VM-lemezkép-verzió törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/read | Megosztott VM-lemezkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/replicate/action | Megosztott VM-lemezkép-verzió replikálása a célrégiókba |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/write | Hozzon létre egy új megosztott VM-lemezkép-verziót, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft.Compute/sharedVMImages/write | Új megosztott VM-lemezkép létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/skus/read | Az előfizetéssel elérhető Microsoft Compute-termékváltozatok listájának beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/beginGetAccess/action | A SAS URI-t a pillanatkép beolvasása blobhozzáféréshez |
> | Műveletek | Microsoft.Compute/snapshots/delete | A pillanatkép törlése |
> | Műveletek | Microsoft.Compute/snapshots/endGetAccess/action | A pillanatkép SAS URI-t visszavonása |
> | Műveletek | Microsoft.Compute/snapshots/read | Pillanatkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/write | Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése |
> | Műveletek | Microsoft.Compute/virtualMachines/capture/action | Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre |
> | Műveletek | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | A virtuális gép blobalapú lemezeinek konvertálása felügyelt lemezekké |
> | Műveletek | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Műveletek | Microsoft.Compute/virtualMachines/delete | Törli a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/delete | Törli a virtuálisgép-bővítményt |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/read | Egy virtuálisgép-bővítmény tulajdonságait olvassa be |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/write | Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt |
> | Műveletek | Microsoft.Compute/virtualMachines/generalize/action | Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez |
> | Műveletek | Microsoft.Compute/virtualMachines/instanceView/read | A virtuális gép és az erőforrásai részletes futási állapotát olvassa be |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |
> | Műveletek | Microsoft.Compute/virtualMachines/performMaintenance/action | Karbantartási művelet végrehajtása a virtuális gépen. |
> | Műveletek | Microsoft.Compute/virtualMachines/powerOff/action | A virtuális gép kikapcsolása. Vegye figyelembe, hogy a virtuális gép továbbra is felszámítjuk. |
> | Műveletek | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuális gép metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Műveletek | Microsoft.Compute/virtualMachines/redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachines/reimage/action | Különbséglemezt használó virtuális gép rendszerképét állítja alaphelyzetbe. |
> | Műveletek | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Műveletek | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |
> | Műveletek | Microsoft.Compute/virtualMachines/write | Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | A virtuálisgép-méretezési csoport példányaihoz hozzárendelt számítási erőforrások kikapcsolása és felszabadítása  |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete | A virtuálisgép-méretezési csoport törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete/action | A virtuálisgép-méretezési csoport példányainak törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Egy virtuálisgép-méretezési csoport bővítménye tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Egy virtuálisgép-méretezési csoport egy új bővítményének létrehozása, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Service Fabric-beli virtuálisgép-méretezési csoport platformfrissítési tartományainak manuális bejárása egy elakadt függőben lévő frissítés befejezéséhez |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | A virtuálisgép-méretezési csoport példánynézetének beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Egy virtuálisgép-méretezési csoport összes hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Virtuálisgép-méretezési csoport gépein végrehajtott operációsrendszer-frissítések előzményeinek beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | A virtuálisgép-méretezési csoport példányai tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | A virtuálisgép-méretezési csoport példányainak kikapcsolása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuálisgép-méretezési csoport metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Egy virtuálisgép-méretezési csoport összes nyilvános IP-címe tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/read | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | A virtuálisgép-méretezési csoport példányainak ismételt üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Ezzel a művelettel alaphelyzetbe állíthatja a virtuálisgép-méretezési csoport példányainak rendszerképét |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Egy virtuálisgép-méretezési csoport példányai összes lemeze (operációsrendszer-lemeze és adatlemeze) rendszerképének alaphelyzetbe állítása  |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/restart/action | Virtuálisgép-méretezési csoport példányainak újraindítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Ezzel a művelettel megszakítja a virtuálisgép-méretezési csoport működés közbeni frissítését |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/scale/action | Annak ellenőrzése, hogy van-e olyan meglévő virtuálisgép-méretezési csoport, mely horizontálisan le- vagy felskálázható a megadott példányszámra |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/skus/read | Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatainak listázása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/start/action | Virtuálisgép-méretezési csoport példányainak elindítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Virtual Machine Scale Set használatával létrehozott nyilvános IP-cím tulajdonságainak beolvasása. Virtuálisgép-méretezési csoportot hozhat létre egy nyilvános IP konfigurációnként (magánhálózati IP) |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Virtual Machine Scale Set használatával létrehozott hálózati adapter egy vagy minden IP-konfiguráció tulajdonságainak beolvasása. IP-konfigurációk magánhálózati IP-címek képviseli |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Egy virtuálisgép-méretezési csoportban létrehozott virtuális gép egy vagy minden hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy méretezési csoportbeli virtuális gép metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány ismételt üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | A művelet alaphelyzetbe állítja a virtuálisgép-méretezési csoportban lévő virtuálisgép-példány rendszerképét. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példány összes lemeze (operációsrendszer-lemeze és adatlemeze) rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányon. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Egy virtuálisgép-méretezési csoportban lévő virtuális gép tulajdonságainak frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/write | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Consumption/balances/read | A felügyeleti csoport egy számlázási időszak összefoglaló kihasználtság listája. |
> | Műveletek | Microsoft.Consumption/budgets/read | Az előfizetés vagy a felügyeleti csoport költségvetése listája. |
> | Műveletek | Microsoft.Consumption/budgets/write | Hoz létre, frissíthet vagy törölhet egy előfizetést vagy a felügyeleti csoport költségvetéseket. |
> | Műveletek | Microsoft.Consumption/marketplaces/read | Nagyvállalati szerződések és WebDirect-előfizetések hatókör Piactéri erőforrás használati adatait listázza. |
> | Műveletek | Microsoft.Consumption/operations/read | Erőforrás-szolgáltató Microsoft.Consumption minden támogatott műveletek listázása. |
> | Műveletek | Microsoft.Consumption/pricesheets/read | Az előfizetés vagy a felügyeleti csoport Pricesheets adatok listája. |
> | Műveletek | Microsoft.Consumption/reservationDetails/read | Foglalási sorrend, illetve a felügyeleti csoportok a fenntartott példányok a szalagfelhasználási adatok listája. A részletes adatok példányonként napi szinten történik. |
> | Műveletek | Microsoft.Consumption/reservationRecommendations/read | Egy előfizetéshez tartozó fenntartott példányokat egyetlen vagy megosztott javaslatok listája. |
> | Műveletek | Microsoft.Consumption/reservationSummaries/read | Foglalási sorrend, illetve a felügyeleti csoportok a fenntartott példányok összefoglaló kihasználtsága a listában. Az összefoglaló adatokat, mint a havi vagy napi szinten. |
> | Műveletek | Microsoft.Consumption/reservationTransactions/read | A tranzakciós előzményekben a fenntartott példányok által a felügyeleti csoportok listája. |
> | Műveletek | Microsoft.Consumption/tenants/register/action | Az egy bérlő által Microsoft.Consumption hatókör művelet regisztrálása. |
> | Műveletek | Microsoft.Consumption/terms/read | A használati egy előfizetést vagy felügyeleti csoport listája. |
> | Műveletek | Microsoft.Consumption/usageDetails/read | Nagyvállalati szerződések és WebDirect-előfizetések hatókör használati adatait listázza. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Egy adott tároló naplóinak beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/delete | Egy adott tárolócsoport törlése. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | A tárolócsoport diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | A tárolócsoport diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | A tárolócsoport elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/read | Az összes tárolócsoport beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/restart/action | Egy adott tárolócsoport újraindul. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/stop/action | Egy adott tárolócsoport leáll. A számítási erőforrások fel kell szabadítani, és a számlázása le fog állni. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/write | Egy adott tárolócsoport létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ContainerInstance/register/action | A tárolópéldány erőforrás-szolgáltatójára való előfizetés regisztrálása és a tárolócsoportok létrehozásának engedélyezése. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerRegistry/checkNameAvailability/read | Ellenőrzi, hogy a tárolóregisztrációs adatbázis nevét használható. |
> | Műveletek | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.ContainerRegistry értesíti, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.ContainerRegistry/locations/operationResults/read | Egy aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/operations/read | Felsorolja az összes, a rendelkezésre álló Azure Container Registry REST API-műveletek |
> | Műveletek | Microsoft.ContainerRegistry/register/action | A tároló beállításjegyzék erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi a tárolóregisztrációs adatbázis létrehozását. |
> | Műveletek | Microsoft.ContainerRegistry/registries/delete | Töröl egy tárolóregisztrációs adatbázisba. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Egy tároló-beállításjegyzéket egy event grid-szűrő törlése. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/read | A megadott event grid-szűrő tulajdonságainak beolvassa vagy listázza a megadott tárolóregisztrációs összes event grid szűrő. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Létrehozza vagy frissíti a tároló-beállításjegyzék egy event grid-szűrő a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/importImage/action | Kép importálása a container Registry-adatbázisban a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listCredentials/action | Megjeleníti a megadott tárolóregisztrációs adatbázis bejelentkezési hitelesítő adatait. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listPolicies/read | A megadott tárolóregisztrációs szabályzatok listája |
> | Műveletek | Microsoft.ContainerRegistry/registries/listUsages/read | A megadott tároló beállításjegyzék – kvótahasználat sorolja fel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/operationStatuses/read | A beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft ContainerRegistry elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.ContainerRegistry/registries/read | A megadott tárolóregisztrációs adatbázis tulajdonságainak beolvassa vagy listázza a megadott erőforráscsoport vagy előfizetés alapján tároló-beállításjegyzékek. |
> | Műveletek | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Újra létrehozza a megadott tárolóregisztrációs adatbázis bejelentkezési hitelesítő adatainak egyikét. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/delete | Törli a replikációt egy tárolóregisztrációs adatbázisba. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | A replikáció aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/read | A megadott replikációs tulajdonságainak beolvassa vagy listázza az adott tároló-beállításjegyzék minden replikációk. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/write | Létrehozza vagy frissíti a replikációt egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/updatePolicies/write | A házirendek a megadott tároló-beállításjegyzék frissítése |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/delete | Webhook törlése egy tárolóregisztrációs adatbázisba. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | A webhook a konfigurációs szolgáltatás URI-t és az egyéni fejlécek beolvasása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | A megadott webhook legutóbbi események listája. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Egy webhook aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Egy pingelés esemény kell küldeni a webhook aktiválja. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/read | A megadott webhook tulajdonságainak beolvassa vagy listázza a webhookokat a megadott tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/write | Létrehozza vagy frissíti a tárolóregisztrációs webhook a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/write | Létrehozza vagy frissíti a tároló-beállításjegyzék a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerService/containerServices/delete | Tárolószolgáltatás törlése |
> | Műveletek | Microsoft.ContainerService/containerServices/read | A tárolószolgáltatás beolvasása |
> | Műveletek | Microsoft.ContainerService/containerServices/write | Létrehoz egy új tároló szolgáltatást, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.ContainerService/locations/operationresults/read | Egy aszinkron művelet eredményének állapotát olvassa be |
> | Műveletek | Microsoft.ContainerService/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Műveletek | Microsoft.ContainerService/locations/orchestrators/read | Sorolja fel az támogatott vezénylők |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Egy felügyelt fürt hozzáférési profil beolvasása lista hitelesítő adatok használatával szerepkörnév alapján |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/read | Egy felügyelt fürt hozzáférési profil beolvasása a szerepkör neve szerint |
> | Műveletek | Microsoft.ContainerService/managedClusters/delete | Egy felügyelt fürt törlése |
> | Műveletek | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Egy felügyelt fürt a clusterAdmin hitelesítő adatainak listázása |
> | Műveletek | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Egy felügyelt fürt a clusterUser hitelesítő adatainak listázása |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Egy felügyelt fürt erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | A felügyelt fürt erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/logDefinitions/read | Naplók beolvasása felügyelt fürt. |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Felügyelt fürt elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.ContainerService/managedClusters/read | Felügyelt fürt létrehozása |
> | Műveletek | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | A fürt frissítési profiljának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/write | Létrehoz egy új felügyelt fürtöt, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.ContainerService/operations/read | Microsoft.ContainerService erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.ContainerService/register/action | Az előfizetés regisztrálása a Microsoft.ContainerService erőforrás-szolgáltató |
> | Műveletek | Microsoft.ContainerService/unregister/action | Előfizetés regisztrációjának Microsoft.ContainerService erőforrás-szolgáltatónál |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContentModerator/applications/delete | Törlési művelet |
> | Műveletek | Microsoft.ContentModerator/applications/listSecrets/action | Titkos kulcsok listázása |
> | Műveletek | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Olvassa el a jogkivonatok az egyszeri bejelentkezés |
> | Műveletek | Microsoft.ContentModerator/applications/read | Olvasási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/listCommunicationPreference/action | Kommunikációs beállítások listázása |
> | Műveletek | Microsoft.ContentModerator/operations/read | olvasási műveletek |
> | Műveletek | Microsoft.ContentModerator/updateCommunicationPreference/action | Kommunikációs beállítások frissítése |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CustomerInsights/hubs/adobemetadata/action | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Adobe metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/adobemetadata/read | Bármely Azure-ügyfél Insights Adobe metaadatok olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Bármely Azure-ügyfél Insights közös hozzáférésű Jogosultságkód házirend törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Olvassa el a bármely Azure a Customer Insights közös hozzáférési aláírások házirendje |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Azure Customer Insights megosztott hozzáférési aláírást szabályzat elsődleges kulcs újragenerálása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Azure Customer Insights megosztott hozzáférési aláírást szabályzat másodlagos kulcs újragenerálása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Hozzon létre vagy bármely Azure-ügyfél Insights közös hozzáférésű Jogosultságkód házirend frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure Customer Insights-összekötő aktiválása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure Customer Insights-összekötő aktiválása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/delete | Bármely Azure Customer Insights-összekötő törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Bármely Azure Customer Insights-összekötő futásidejű állapot beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Bármely Azure-ügyfél Insights Connector leképezés aktiválása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Bármely Azure-ügyfél Insights Connector leképezés törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Olvassa el a minden Azure Customer Insights Connector leképezési művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Bármely Azure-ügyfél Insights Connector leképezés olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Connector leképezés |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/operations/read | Olvassa el a minden Azure Customer Insights-összekötő művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/read | Olvassa el az összes Azure-ügyfél Insights-összekötő |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Connector hitelesítési átjárókapcsolat |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/update/action | Bármely Azure-ügyfél Insights összekötő frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/write | Létrehozás vagy frissítés bármely Azure Customer Insights-összekötő |
> | Műveletek | Microsoft.CustomerInsights/hubs/crmmetadata/action | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Crm-metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/crmmetadata/read | Olvassa el az Azure-ügyfél Insights Crm-metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/delete | Bármely Azure-ügyfél Insights központ törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/delete | Bármely Azure-ügyfél Insights Gdpr törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/read | Bármely Azure-ügyfél Insights Gdpr olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/write | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Gdpr |
> | Műveletek | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Azure-ügyfél Insights Hub számlázási kreditjei |
> | Műveletek | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Azure-ügyfél Insights Hub számlázási előzmények beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/delete | Bármely Azure-ügyfél Insights lemezkép törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/read | Olvassa el az Azure-ügyfél Insights képet |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/write | Bármely Azure-ügyfél Insights lemezkép létrehozása vagy frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/delete | Customer Insights azure kölcsönhatások törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/operations/read | Olvassa el a minden Azure Customer Insights kapcsolati művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/read | Olvassa el az Azure-ügyfél Insights beavatkozás |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Kapcsolati hivatkozások javasolunk bármely Azure-ügyfél Insights interakciók |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/write | Létrehozása vagy frissítése az Azure-ügyfél Insights beavatkozás |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/delete | Bármely Azure-ügyfél Insights fő teljesítménymutató törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/operations/read | Olvassa el a minden Azure Customer Insights fő teljesítménymutatók művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/read | Bármely Azure-ügyfél Insights fő teljesítménymutató olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Bármely Azure-ügyfél Insights fő teljesítménymutatók újrafeldolgozása |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/write | Létrehozás vagy frissítés bármely Azure-ügyfél Insights fő teljesítménymutató |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/delete | Bármely Azure-ügyfél Insights hivatkozások törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/operations/read | Olvassa el a minden Azure Customer Insights hivatkozások művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/read | Olvassa el az Azure-ügyfél Insights hivatkozásai |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/write | Létrehozása vagy frissítése az Azure-ügyfél hivatkozásai |
> | Műveletek | Microsoft.CustomerInsights/hubs/msemetadata/action | Létrehozás vagy frissítés bármely Azure-ügyfél Insights Mse metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/msemetadata/read | Bármely Azure-ügyfél Insights Mse metaadatok olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/operationresults/read | Azure-ügyfél Insights Hub művelet eredményeinek lekérése |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/delete | Bármely Azure-ügyfél Insights előrejelzéseket törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/operations/read | Olvassa el a minden Azure Customer Insights előrejelzéseket művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/read | Bármely Azure-ügyfél Insights előrejelzéseket olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/write | Létrehozás vagy frissítés bármely Azure-ügyfél előrejelzések |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Bármely Azure-ügyfél Insights prediktív egyezés szabályzat törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Olvassa el a minden Azure Customer Insights prediktív egyezés házirendek művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Bármely Azure-ügyfél Insights prediktív egyezés szabályzat olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Hozzon létre vagy bármely Azure-ügyfél Insights prediktív egyezés szabályzat frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/delete | Bármely Azure-ügyfél Insights profil törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/operations/read | Olvassa el a minden Azure Customer Insights-profilban művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/read | Bármely Azure-ügyfél Insights profil olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/write | Bármely Azure Customer Insights-profilban írása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Az erőforrás elérhető naplók beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Az erőforrás elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.CustomerInsights/hubs/read | Bármely Azure-ügyfél Insights Hub olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Bármely Azure-ügyfél Insights kapcsolati hivatkozások törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Olvassa el a minden Azure Customer Insights kapcsolati hivatkozások művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Olvassa el az Azure-ügyfél Insights kapcsolati hivatkozások |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Létrehozása vagy frissítése az Azure-ügyfél Insights kapcsolati hivatkozások |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/delete | Azure-ügyfél Insights kapcsolatok törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/operations/read | Olvassa el a minden Azure Insights ügyfélkapcsolatok művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/read | Olvassa el az Azure-ügyfél Insights kapcsolatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/write | Létrehozása vagy frissítése az Azure-ügyfél Insights kapcsolatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Bármely Azure-ügyfél Insights Rbac-hozzárendelés törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Olvassa el a minden Azure Customer Insights Rbac-hozzárendelés művelet eredménye |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/read | Olvassa el a minden Azure-ügyfél Insights Rbac-hozzárendelés |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/write | Hozzon létre vagy bármely Azure-ügyfél Insights Rbac-hozzárendelés frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/roles/read | Olvassa el az Azure-ügyfél Insights Rbac-szerepkörök |
> | Műveletek | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Létrehozás vagy frissítés bármely Azure-ügyfél Insights SalesForce-metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Bármely Azure-ügyfél Insights SalesForce metaadatok olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/delete | Bármely Azure Insights ügyfélszegmensek törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Felügyeleti bármely Azure-ügyfél Insight dinamikus szegmensek |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/read | Bármely Azure Insights ügyfélszegmensek olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/static/action | Felügyeleti bármely Azure-ügyfél Insight statikus szegmensek |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/write | Bármely Azure-ügyfél Insights szegmensek létrehozása és módosítása |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Bármely Azure-ügyfél Insights SqlConnectionStrings törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Bármely Azure-ügyfél Insights SqlConnectionStrings olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Létrehozás vagy frissítés bármely Azure-ügyfél Insights SqlConnectionStrings |
> | Műveletek | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Javaslat típus séma létrehozása a mintaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Bármely Azure a Customer Insights hub beállításainak kezelése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/delete | Bármely Azure-ügyfél Insights App nézet törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/read | Olvassa el a minden Azure-ügyfél Insights alkalmazás megtekintése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/write | Létrehozás vagy frissítés bármely Azure-ügyfél Insights alkalmazás megtekintése |
> | Műveletek | Microsoft.CustomerInsights/hubs/widgettypes/read | Olvassa el az olyan Azure-ügyfél Insights alkalmazás Widget típusokat |
> | Műveletek | Microsoft.CustomerInsights/hubs/write | Hozzon létre vagy bármely Azure-ügyfél Insights Hub frissítése |
> | Műveletek | Microsoft.CustomerInsights/operations/read | Olvassa el az Azure-ügyfél Insights Api Metadatas |
> | Műveletek | Microsoft.CustomerInsights/register/action | A Customer Insights erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és a Customer Insights-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.CustomerInsights/unregister/action | A Customer Insights erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Lehetőség a visszaküldési begyűjtések időpontjának lefoglalására. |
> | Műveletek | Microsoft.DataBox/jobs/cancel/action | Egy folyamatban lévő megrendelés törlése. |
> | Műveletek | Microsoft.DataBox/jobs/copyLogsUri/action | Az egyes célfiókok példánynaplóira mutató URL-cím beolvasása. |
> | Műveletek | Microsoft.DataBox/jobs/delete | A rendelés törlése |
> | Műveletek | Microsoft.DataBox/jobs/downloadShippingLabel/action | A visszaküldési levélcímke letöltése. |
> | Műveletek | Microsoft.DataBox/jobs/listSecrets/action | A megrendeléssel kapcsolatos titkosítatlan titkos kulcsok listázása. |
> | Műveletek | Microsoft.DataBox/jobs/read | Listázás vagy beolvasás a rendelések |
> | Műveletek | Microsoft.DataBox/jobs/reportIssue/action | Jelentés küldése egy megrendeléssel kapcsolatos problémáról. |
> | Műveletek | Microsoft.DataBox/jobs/write | A rendelések létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataBox/locations/availableSkus/action | Ez a metódus a rendelkezésre álló termékváltozatok listáját adja vissza. |
> | Műveletek | Microsoft.DataBox/locations/validateAddress/action | A szállítási cím ellenőrzése és másodlagos címek biztosítása, ha vannak ilyenek. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Databricks/workspaces/delete | Eltávolít egy munkaterületet. |
> | Műveletek | Microsoft.Databricks/workspaces/read | Munkaterületek listájának beolvasása. |
> | Műveletek | Microsoft.Databricks/workspaces/write | Létrehoz egy munkaterületet. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataCatalog/catalogs/delete | Törli a katalógusban. |
> | Műveletek | Microsoft.DataCatalog/catalogs/read | A katalógus vagy előfizetésekhez vagy erőforráscsoportokhoz alatt katalógusok tulajdonságainak lekérése. |
> | Műveletek | Microsoft.DataCatalog/catalogs/write | Katalógus létrehozása vagy frissítése, a címkék és a katalógus tulajdonságait. |
> | Műveletek | Microsoft.DataCatalog/checkNameAvailability/action | Bérlői katalógus név rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.DataCatalog/operations/read | Microsoft.DataCatalog erőforrás-szolgáltató elérhető műveleteinek listázása. |
> | Műveletek | Microsoft.DataCatalog/register/action | Előfizetés regisztrálása a Microsoft.DataCatalog erőforrás-szolgáltatónál. |
> | Műveletek | Microsoft.DataCatalog/unregister/action | Előfizetés Microsoft.DataCatalog erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataFactory/datafactories/activitywindows/read | Beolvassa a tevékenység Windows adat-előállító megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Tevékenység Windows beolvassa a folyamat tevékenységének megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Tevékenység Windows beolvassa a folyamat a megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/delete | Törli a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Felfüggeszti a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/read | Beolvassa a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Folytatja a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/update/action | Frissíti a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/write | Létrehozza vagy frissíti a bármely folyamatba. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Windows tevékenység beolvassa az adatkészlethez megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/delete | Töröl minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/read | Minden olyan adatkészletben beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Beolvassa a szelet-futtassa a megadott kezdési időponttal az adott adatkészlet. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/read | Az Adatszelet lekérdezi a megadott időszakban. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/write | Az Adatszelet állapotának frissítése. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/write | Létrehozza vagy frissíti az minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/datafactories/delete | A Data Factory törlése. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Beolvassa a kapcsolati adatokat az átjárókat. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/delete | Átjáró törlése. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | A hitelesítési kulcsok felsorolja az átjárókat. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/read | Átjáró beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Az átjáró hitelesítése kulcsainak újragenerálása. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/write | Létrehozza vagy frissíti az átjárókat. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/delete | Törli az összes társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/read | Beolvassa az összes társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/write | Létrehozza vagy frissíti az összes társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Datafactories elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.DataFactory/datafactories/read | Olvassa be az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/datafactories/runs/loginfo/read | Beolvassa egy SAS URI-t a naplókat tartalmazó blob-tárolóba. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/delete | Töröl minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/read | Minden olyan adatkészletben beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/write | Létrehozza vagy frissíti az minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/datafactories/write | Létrehozza vagy frissíti az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/factories/cancelpipelinerun/action | Megszakítja a folyamatfuttatás azokat a futtatási azonosítót. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/delete | Töröl minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/read | Minden olyan adatkészletben beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/write | Létrehozza vagy frissíti az minden olyan adatkészletben. |
> | Műveletek | Microsoft.DataFactory/factories/delete | A Data Factory törlése. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/delete | Törli az összes integrációs modul. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integration Runtime-kapcsolati adatok beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integrációs modul állapotának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | A hitelesítési kulcsok bármilyen saját üzemeltetésű integrációs sorolja fel. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Lekéri a Monitorozási adatok bármely integrációs modul. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | A csomópont törli a megadott integrációs modulok esetében. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | A megadott csomópont az integrációs modul IP-címét adja vissza. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Frissíti egy saját üzemeltetésű Integration Runtime csomópontja. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/read | Bármilyen saját üzemeltetésű integrációs beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | A megadott Integration Runtime hitelesítési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/start/action | Bármilyen saját üzemeltetésű integrációs elindul. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Leállítja az összes integrációs modul. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | A hitelesítő adatokat szinkronizálja az adott integrációs modul. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Frissíti a megadott integrációs modult. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/write | Létrehozza vagy frissíti az összes integrációs modul. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/delete | Törli a társított szolgáltatást. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/read | Olvasási társított szolgáltatást. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/write | Társított szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Beolvassa a megadott folyamatfuttatási azonosító. a tevékenység futtatása |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Megszakítja a folyamatfuttatás azokat a futtatási azonosítót. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | A megadott folyamat a tevékenység futtatása lekérdezések futtatása azonosítója. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/read | A Folyamatfuttatások beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/createrun/action | Futtatás hoz létre a folyamatot. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/delete | Törli a folyamatot. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | A folyamat futásának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/read | Olvassa be a folyamatot. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/write | Létrehozni vagy frissíteni a folyamatot |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Az előállítók elérhető naplók beolvasása |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Az előállítók elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.DataFactory/factories/querypipelineruns/action | A Folyamatfuttatások lekérdezi. |
> | Műveletek | Microsoft.DataFactory/factories/querytriggerruns/action | Lekérdezi az eseményindító-futtatások. |
> | Műveletek | Microsoft.DataFactory/factories/read | A Data Factory beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggerruns/read | Az eseményindító-futtatások beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/delete | Törli az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/read | Minden Trigger beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/start/action | Minden Trigger elindul. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/stop/action | Leállítja az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/triggerruns/read | Az eseményindító-futtatások beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/write | Létrehozza vagy frissíti az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/write | Létrehozni vagy frissíteni a Data Factory |
> | Műveletek | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfigurálja az előállító a tárházban. |
> | Műveletek | Microsoft.DataFactory/register/action | A Data Factory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása. |
> | Műveletek | Microsoft.DataFactory/unregister/action | A Data Factory erőforrás-szolgáltató az előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Compute-házirend törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Számítási szabályzat adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Hozzon létre, vagy egy számítási szabályzat frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Egy DataLakeAnalytics fiókot egy Data Lake Store-fiók leválasztása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | A DataLakeAnalytics fiók társított Data Lake Store-fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Hozzon létre vagy nem frissíthető a DataLakeAnalytics fiók társított Data Lake Store-fiók. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics fiók törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Létrehozása vagy frissítése egy tűzfalszabályt. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Fiók DataLakeAnalytics művelet eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A diagnosztikai beállításokat a DataLakeAnalytics fiók beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy a DataLakeAnalytics fiók diagnosztikai beállításainak frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | A DataLakeAnalytics fiókhoz elérhető naplók beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | A DataLakeAnalytics fiókhoz elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/read | Meglévő DataLakeAnalytics fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | A storage-tárolók egy csatolt tárfiók DataLakeAnalytics fiók SAS-tokeneket listája. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Tárolók DataLakeAnalytics fiók társított Storage-fiók beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | A DataLakeAnalytics fiók egy Storage-fiók leválasztása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | A DataLakeAnalytics fiók társított Storage-fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Hozzon létre vagy nem frissíthető a DataLakeAnalytics fiók társított Storage-fiók. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által beküldött feladatok megszakítása engedélyezheti. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/write | Hozzon létre, vagy egy DataLakeAnalytics fiók frissítéséhez. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/capability/read | Előfizetés vonatkozó információk lekérése DataLakeAnalytics használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | A DataLakeAnalytics fióknév elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/operationResults/read | Fiók DataLakeAnalytics művelet eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/operations/read | A DataLakeAnalytics elérhető műveletek beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/register/action | A DataLakeAnalytics előfizetés regisztrálása. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeStore/accounts/delete | Egy Data Lake Store-fiók törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Engedélyezze a KeyVault egy Data Lake Store-fiókot. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Egy EventGrid szűrő törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Egy EventGrid-szűrő beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Létrehozása vagy frissítése egy EventGrid-szűrőt. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/write | Létrehozása vagy frissítése egy tűzfalszabályt. |
> | Műveletek | Microsoft.DataLakeStore/accounts/operationResults/read | A Data Lake Store-fiók művelet eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A Data Lake Store-fiók diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy a Data Lake Store-fiók diagnosztikai beállításainak frissítése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | A Data Lake Store-fiókhoz elérhető naplók beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | A rendelkezésre álló metrikák beolvasása a Data Lake Store-fiókot. |
> | Műveletek | Microsoft.DataLakeStore/accounts/read | Egy meglévő Data Lake Store-fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a Data Lake Store-Microsoft.Authorization/roleAssignments/write megadásakor Superuser. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Törölje egy megbízható identitásszolgáltatóra. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Egy megbízható identitásszolgáltatóra adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Létrehozása vagy frissítése egy megbízható identitásszolgáltatóra. |
> | Műveletek | Microsoft.DataLakeStore/accounts/write | Hozzon létre, vagy egy Data Lake Store-fiók frissítéséhez. |
> | Műveletek | Microsoft.DataLakeStore/locations/capability/read | Előfizetés vonatkozó információk lekérése Data Lake Store használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeStore/locations/checkNameAvailability/action | A Data Lake Store-fióknév elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.DataLakeStore/locations/operationResults/read | A Data Lake Store-fiók művelet eredményének beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/operations/read | A Data Lake Store használható műveletek beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/register/action | A Data Lake Store előfizetés regisztrálása. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataMigration/locations/operationResults/read | 202-es (Elfogadva) válaszhoz kapcsolódó sokáig futó művelet állapotának lekérése |
> | Műveletek | Microsoft.DataMigration/locations/operationStatuses/read | 202-es (Elfogadva) válaszhoz kapcsolódó sokáig futó művelet állapotának lekérése |
> | Műveletek | Microsoft.DataMigration/register/action | Az előfizetés regisztrálása az Azure Database Migration Service szolgáltatójával |
> | Műveletek | Microsoft.DataMigration/services/checkStatus/action | A szolgáltatás telepítésének és futásának ellenőrzése |
> | Műveletek | Microsoft.DataMigration/services/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/accessArtifacts/action | A projektösszetevőkre vonatkozó GET vagy PUT műveletek elvégzéséhez használható URL létrehozása |
> | Műveletek | Microsoft.DataMigration/services/projects/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/files/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/files/read | Olvasható információk erőforrásokról |
> | Műveletek | Microsoft.DataMigration/services/projects/files/read/action | Szerezzen be egy URL-címet, amellyel olvashatja vagy a fájl tartalmát |
> | Műveletek | Microsoft.DataMigration/services/projects/files/readWrite/action | Szerezzen be egy URL-címet, amellyel olvashatja vagy írhatja a fájl tartalmát |
> | Műveletek | Microsoft.DataMigration/services/projects/files/write | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Műveletek | Microsoft.DataMigration/services/projects/read | Olvasható információk erőforrásokról |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/cancel/action | A feladat megszakítása, ha jelenleg fut |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/read | Olvasható információk erőforrásokról |
> | Műveletek | Microsoft.DataMigration/services/projects/tasks/write | Feladatok futtatása Azure Database Migration Service-feladatok |
> | Műveletek | Microsoft.DataMigration/services/projects/write | Feladatok futtatása Azure Database Migration Service-feladatok |
> | Műveletek | Microsoft.DataMigration/services/read | Olvasható információk erőforrásokról |
> | Műveletek | Microsoft.DataMigration/services/slots/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft.DataMigration/services/slots/read | Olvasható információk erőforrásokról |
> | Műveletek | Microsoft.DataMigration/services/slots/write | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Műveletek | Microsoft.DataMigration/services/start/action | A DMS szolgáltatás elindítása az áttelepítések feldolgozásának ismételt engedélyezéséhez |
> | Műveletek | Microsoft.DataMigration/services/stop/action | A DMS-szolgáltatás leállítása a költségek csökkentése érdekében |
> | Műveletek | Microsoft.DataMigration/services/write | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Műveletek | Microsoft.DataMigration/skus/read | DMS-erőforrások által támogatott SKU-k listájának lekérése. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DBforMySQL/locations/performanceTiers/read | A teljesítményszintek elérhető listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/performanceTiers/read | A teljesítményszintek elérhető listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/configurations/read | Lekérdezi a megadott konfiguráció tulajdonságainak vagy a kiszolgáló konfigurációk listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/configurations/write | Frissítse az értéket a megadott konfiguráció |
> | Műveletek | Microsoft.DBforMySQL/servers/delete | Töröl egy meglévő kiszolgálóra. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/delete | Törli a meglévő tűzfalszabály. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/read | Vissza a tűzfal egy kiszolgálóra vonatkozó szabályok vagy a megadott tűzfalszabály tulajdonságainak lekérése. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/write | Egy olyan tűzfalszabályt hoz létre a megadott paraméterek vagy a meglévő szabály módosítása. |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás disagnostic beállításának beolvasása |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrikák elérhető adatbázisok |
> | Műveletek | Microsoft.DBforMySQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/recoverableServers/read | A MySQL-kiszolgáló helyreállítható adatok visszaadása |
> | Műveletek | Microsoft.DBforMySQL/servers/updateConfigurations/action | A megadott kiszolgáló frissítési konfigurációi |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Törli a meglévő virtuális hálózati szabály |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Vissza a virtuális hálózati szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak lekérése. |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Egy virtuális hálózati szabályt hoz létre a megadott paramétereket, vagy a Tulajdonságok vagy címkék a megadott virtuális hálózati szabály frissítése. |
> | Műveletek | Microsoft.DBforMySQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott kiszolgáló frissítése. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | A teljesítményszintek elérhető listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/performanceTiers/read | A teljesítményszintek elérhető listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/configurations/read | Lekérdezi a megadott konfiguráció tulajdonságainak vagy a kiszolgáló konfigurációk listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/configurations/write | Frissítse az értéket a megadott konfiguráció |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/delete | Töröl egy meglévő kiszolgálóra. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Törli a meglévő tűzfalszabály. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Vissza a tűzfal egy kiszolgálóra vonatkozó szabályok vagy a megadott tűzfalszabály tulajdonságainak lekérése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Egy olyan tűzfalszabályt hoz létre a megadott paraméterek vagy a meglévő szabály módosítása. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás disagnostic beállításának beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Naplók beolvasása a Postgres-kiszolgálók |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrikák elérhető adatbázisok |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/queryTexts/action | A lekérdezés szöveget adja vissza |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | A PostgreSQL-kiszolgáló helyreállítható adatok visszaadása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | A kiszolgáló fenyegetésészlelési szabályzatát a megadott kiszolgálón konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgáló egy adott kiszolgálóhoz fenyegetésészlelési szabályzatát módosítása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Lekérdezési statisztikák a leggyakoribb lekérdezések listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | A megadott kiszolgáló frissítési konfigurációi |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Törli a meglévő virtuális hálózati szabály |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Vissza a virtuális hálózati szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak lekérése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Egy virtuális hálózati szabályt hoz létre a megadott paramétereket, vagy a Tulajdonságok vagy címkék a megadott virtuális hálózati szabály frissítése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott kiszolgáló frissítése. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze, hogy ha IotHub name érhető el |
> | Műveletek | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy ha a kiépítési szolgáltatás érhető el |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Ellenőrző kód létrehozása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Hozzon létre vagy tanúsítványának frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Az IotHub-bérlői erőforrás törlése |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Az EventHub fogyasztói csoport törlése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoport beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Az EventHub fogyasztói csoport létrehozása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Az IotHub-bérlő stats erőforrás beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Az IotHub-bérlőkulcsot beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Feladat küldése megadott IotHub részleteinek beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | IotHub-bérlői kulcsok beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Az IotHub-szolgáltatás a rendelkezésre álló naplódefinícióinak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Az IotHub-Service elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Kvóta metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Read | Az IotHub-bérlői erőforrás beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Egy üzenet, szemben az összes meglévő útvonal tesztelése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Egy üzenet, szemben a megadott vizsgálati útvonal tesztelése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Az IotHub minden útválasztási végpontok állapotának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Write | Hozzon létre vagy az IotHub-bérlői erőforrás frissítése |
> | Műveletek | Microsoft.Devices/ElasticPools/metricDefinitions/read | Az IotHub-Service elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Ellenőrző kód létrehozása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Write | Hozzon létre vagy tanúsítványának frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/Delete | IotHub-erőforrás törlése |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Event Grid-szűrő törlése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Read | Event Grid-szűrő beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Write | Új létrehozása vagy meglévő Event Grid-szűrő módosítása |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Az EventHub fogyasztói csoport törlése |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoport beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Az EventHub fogyasztói csoport létrehozása |
> | Műveletek | Microsoft.Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | A megadott névvel az IotHub-kulcs lekérése |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub statisztikájának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/jobs/Read | Feladat küldése megadott IotHub részleteinek beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/listkeys/Action | Az összes IotHub-kulcsok beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/logDefinitions/read | Az IotHub-szolgáltatás a rendelkezésre álló naplódefinícióinak beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/metricDefinitions/read | Az IotHub-Service elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Devices/iotHubs/operationresults/Read | (Elavult API) művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/quotaMetrics/Read | Kvóta metrikáinak beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Read | Az IotHub-erőforrás(ok) beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testall/Action | Egy üzenet, szemben az összes meglévő útvonal tesztelése |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testnew/Action | Egy üzenet, szemben a megadott vizsgálati útvonal tesztelése |
> | Műveletek | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Az IotHub minden útválasztási végpontok állapotának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/skus/Read | Érvényes IotHub termékváltozatok beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Write | Hozzon létre vagy IotHub erőforrás frissítése |
> | Műveletek | Microsoft.Devices/locations/operationresults/Read | Első hely alapú művelet eredménye |
> | Műveletek | Microsoft.Devices/operationresults/Read | Művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/operations/Read | Az összes ResourceProvider műveletek beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Ellenőrző kód létrehozása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Write | Hozzon létre vagy tanúsítványának frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/Delete | IotDps erőforrás törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/keys/listkeys/Action | A kulcsnév IotDps kulcsok beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/listkeys/Action | Az összes IotDps kulcsok beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/logDefinitions/read | A kiépítési szolgáltatás a rendelkezésre álló naplódefinícióinak beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/metricDefinitions/read | A kiépítési szolgáltatás elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Devices/provisioningServices/operationresults/Read | A DPS művelet eredményének beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/Read | IotDps erőforrás lekérése |
> | Műveletek | Microsoft.Devices/provisioningServices/skus/Read | Érvényes IotDps termékváltozatok beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/Write | IotDps erőforrás létrehozása |
> | Műveletek | Microsoft.Devices/register/action | Az IotHub erőforrás-szolgáltató előfizetésének regisztrálása és az IotHub-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.Devices/register/action | Az IotHub erőforrás-szolgáltató előfizetésének regisztrálása és az IotHub-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.Devices/usages/Read | Használat részletei az előfizetés beszerzése a szolgáltató. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevTestLab/labCenters/delete | Törölje a labor-erőforrásokat. |
> | Műveletek | Microsoft.DevTestLab/labCenters/read | Olvassa el a labor-erőforrásokat. |
> | Műveletek | Microsoft.DevTestLab/labCenters/write | Hozzáadása vagy módosítása a labor-erőforrásokat. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Olvassa el az azure resource manager-sablonok. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Létrehoz egy adott összetevőre ARM-sablon, feltölti a szükséges fájlokat a tárfiókba, és érvényesíti a létrehozott összetevő. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Olvassa el az eltéréseket. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/delete | Összetevő forrásai törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/read | Összetevő forrásai olvasni. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/write | Hozzáadása vagy módosítása összetevő forrásai. |
> | Műveletek | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Jogcím egy véletlenszerű igényelhető virtuális gép a tesztkörnyezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/read | Olvassa el a költségek. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/write | Hozzáadása vagy módosítása a költségek. |
> | Műveletek | Microsoft.DevTestLab/labs/CreateEnvironment/action | Virtuális gépek létrehozása a lab-ben. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/delete | Egyéni rendszerképek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/read | Olvassa el az egyéni lemezképek. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/write | Hozzáadása vagy módosítása az egyéni lemezképek. |
> | Műveletek | Microsoft.DevTestLab/labs/delete | Törölje a labs. |
> | Műveletek | Microsoft.DevTestLab/labs/ExportResourceUsage/action | A lab erőforrás-használat exportálja a storage-fiókra |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/delete | Törölje a képletek. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a képletek. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/write | Adjon hozzá vagy módosíthatja a képleteket. |
> | Műveletek | Microsoft.DevTestLab/labs/galleryImages/read | Olvassa el a katalógus rendszerképeinek letöltéséhez. |
> | Műveletek | Microsoft.DevTestLab/labs/GenerateUploadUri/action | URI generálása az egyéni lemezképek feltöltése a labor. |
> | Műveletek | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Virtuális gép importálása másik labor. |
> | Műveletek | Microsoft.DevTestLab/labs/ListVhds/action | Egyéni rendszerkép létrehozásához rendelkezésre álló lemezképek listázása. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/delete | Notificationchannels törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Értesítés küldése a megadott csatornában. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/read | Olvassa el a notificationchannels. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/write | Hozzáadása vagy módosítása notificationchannels. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Kiértékeli a labor házirend. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/delete | Törölje a házirendeket. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/read | Olvassa el a házirendeket. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/write | Adjon hozzá vagy módosíthatja a szabályzatokat. |
> | Műveletek | Microsoft.DevTestLab/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/delete | Ütemezések törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/Execute/action | Hajtsa végre az ütemezés szerint. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Felsorolja az összes alkalmazható ütemezések |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/read | Olvassa el az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/write | Adjon hozzá vagy ütemezésének módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/delete | Szolgáltatás indák törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/read | Olvassa el a szolgáltatás indák. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/write | Adja hozzá, vagy módosítsa a szolgáltatás indák. |
> | Műveletek | Microsoft.DevTestLab/labs/users/delete | Felhasználói profilok törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Attach/action | Csatolja, és a bérletet, a lemez a virtuális gép létrehozásához. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/delete | Törölje a lemezeket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Detach/action | Válassza le, és a lemez a virtuális géphez csatolt bérlet. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/read | Olvassa el a lemezeket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/write | Hozzáadása vagy módosítása a lemezeket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/read | Környezetek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/users/read | Olvassa el a felhasználói profilokat. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/delete | Titkos kódok törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/read | Titkos kódok olvasását. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/write | Hozzáadása vagy módosítása a titkos kulcsok. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Szolgáltatás-hálók törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Felsorolja az összes alkalmazható ütemezések |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Olvassa el a szolgáltatás hálókhoz. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ütemezések törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Hajtsa végre az ütemezés szerint. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Olvassa el az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adjon hozzá vagy ütemezésének módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Indítsa el a service fabric. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zastaví service fabric. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adja hozzá, vagy módosítsa a szolgáltatás hálókhoz. |
> | Műveletek | Microsoft.DevTestLab/labs/users/write | Adja hozzá, vagy módosítsa a felhasználói profilokat. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Új vagy meglévő adatlemez csatolása a virtuális géphez. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Virtuális gép összetevőinek vonatkoznak. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/delete | Törölje a virtuális gépeket. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Válassza le a megadott lemezt a virtuális gépről. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Egy karakterlánc, amely a virtuális gép RDP-fájl tartalmának beolvasása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Felsorolja az összes alkalmazható ütemezések |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/read | Olvassa el a virtuális gépeket. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | A virtuális gép ismételt üzembe helyezése |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Méretezze át a virtuális gép. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Virtuális gép újraindításához. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ütemezések törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Hajtsa végre az ütemezés szerint. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Olvassa el az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adjon hozzá vagy ütemezésének módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Virtuális gép elindítására. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Virtuális gép leállítása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Saját magának adatlemezeket a virtuális gép tulajdonjogának átruházása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Egy meglévő virtuális gép tulajdonjogának kiadás |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/delete | Törölje a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/read | Olvassa el a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/write | Adja hozzá, vagy módosítsa a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/delete | Törölje a virtuálisgép-készleteket. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/read | Olvassa el a virtuálisgép-készleteket. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/write | Adja hozzá, vagy módosítsa a virtuálisgép-készleteket. |
> | Műveletek | Microsoft.DevTestLab/labs/write | Hozzáadása vagy módosítása a labs. |
> | Műveletek | Microsoft.DevTestLab/locations/operations/read | Olvasási műveletek. |
> | Műveletek | Microsoft.DevTestLab/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.DevTestLab/schedules/delete | Ütemezések törlése. |
> | Műveletek | Microsoft.DevTestLab/schedules/Execute/action | Hajtsa végre az ütemezés szerint. |
> | Műveletek | Microsoft.DevTestLab/schedules/read | Olvassa el az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/schedules/Retarget/action | Frissíti egy ütemezés célként megadott erőforrás-azonosító. |
> | Műveletek | Microsoft.DevTestLab/schedules/write | Adjon hozzá vagy ütemezésének módosítása. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DocumentDB/databaseAccountNames/read | A név rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Váltás másik erőforráscsoportra, egy adatbázis-fiók |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | A gyűjtemény metrikadefinícióinak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | A gyűjtemény metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el az adatbázis fiók partíciós kulcs metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Olvassa el az adatbázis fiók partíció metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Adatbázis-fiók partíciók egy gyűjtemény olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Olvassa el az adatbázisfiók partíció szintű használat |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Beolvassa a gyűjtemény használatokban. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Az adatbázis metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Az adatbázis-metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Beolvassa a adatbázis használatokban. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/delete | Törli a database-fiókokat. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Az adatbázisfiók régiók feladatátvételi prioritások módosítása Manuális feladatátvétel művelet végrehajtásához használatos |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | A kapcsolati karakterláncok beolvasása adatbázisfiók |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Egy adatbázis-fiók kulcsainak listázása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Az adatbázis fiók metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metrics/read | Az adatbázis-fiók metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Kapcsolat nélküli módban egy régióban, egy adatbázis-fiók.  |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online egy régióban, egy adatbázis-fiók. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Az aszinkron művelet állapotának olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Olvasási késés metrikák |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/read | Olvassa el a replikáció késése. percentilisei |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Olvassa el a késés metrikák egy adott forrás és cél régióhoz |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Egy adott célrégió olvasási késés metrikái |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Lekéri az elérhető log catageries adatbázisfiók |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | A fiók adatbázis elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/read | Egy adatbázis-fiók beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis csak olvasható kulcsainak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Az adatbázis csak olvasható kulcsainak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Egy adatbázis-fiók kulcsainak rotálása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | A regionális gyűjtemény metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el a regionális adatbázis fiók partíciós kulcs metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Olvassa el a regionális adatbázis fiók partíció metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Regionális adatbázis-fiók partíciók egy gyűjtemény olvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | A régiók és az adatbázis mérőszámai beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/usages/read | Beolvassa az adatbázist fiók használatokban. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/write | Egy adatbázis-fiók frissítéséhez. |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB értesíti, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Olvassa el a deleteVirtualNetworkOrSubnets állapotát aszinkron művelet |
> | Műveletek | Microsoft.DocumentDB/operationResults/read | Az aszinkron művelet állapotának olvasása |
> | Műveletek | Microsoft.DocumentDB/operations/read | A Microsoft documentdb elérhető műveletek beolvasása  |
> | Műveletek | Microsoft.DocumentDB/register/action |  Az előfizetés a Microsoft a DocumentDB erőforrás-szolgáltató regisztrálása |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DomainRegistration/checkDomainAvailability/Action | Ellenőrizze, hogy elérhető és megvásárolható kiadása-e egy tartományhoz |
> | Műveletek | Microsoft.DomainRegistration/domains/Delete | Törölje a meglévő tartományhoz. |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Törölje a tulajdonjog azonosítója |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista tulajdonjog azonosítók |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonjog azonosító beszerzése |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Létrehozás vagy frissítés azonosítója |
> | Műveletek | Microsoft.DomainRegistration/domains/operationresults/Read | Egy művelet beolvasása |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | Tartományok listájának beolvasása |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Műveletek | Microsoft.DomainRegistration/domains/renew/Action | Egy meglévő tartomány megújítása. |
> | Műveletek | Microsoft.DomainRegistration/domains/Write | Új tartomány hozzáadása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.DomainRegistration/generateSsoRequest/Action | Hozzon létre a tartomány vezérlőközpontba aláírási kérelmet. |
> | Műveletek | Microsoft.DomainRegistration/listDomainRecommendations/Action | A lista tartomány javaslatok kulcsszavak alapján beolvasása |
> | Műveletek | Microsoft.DomainRegistration/operations/Read | Az app service tartományregisztráció minden művelet listázása |
> | Műveletek | Microsoft.DomainRegistration/register/action | Az előfizetés a Microsoft Domains erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Szerződés művelet listázása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | Toplevel tartományok beolvasása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | Toplevel lekérése |
> | Műveletek | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Tartomány beszerzési objektum ellenőrzése nélkül elküldené azokat |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | A Microsoft Dynamics Lifecycle Services-projektben, amely egy felhasználóhoz tartozik Microsoft Dynamics AX 2012 R3-próbaverzióra központi telepítések megjelenítése |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Microsoft Dynamics AX 2012 R3-próbaverzióra központi telepítés létrehozása, amely egy felhasználóhoz tartozik Microsoft Dynamics Lifecycle Services projektben. Üzemelő példányok is felügyelhető az Azure felügyeleti portálján |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Olvassa el a Microsoft Dynamics Lifecycle Services-projekthez tartozó összekötők |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Létrehozhat és frissíthet egy Microsoft Dynamics Lifecycle Services-projekthez tartozó összekötők |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/delete | A Microsoft Dynamics Lifecycle Services-projektek, a felhasználóhoz tartozó törlése |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/read | A felhasználóhoz tartozó Microsoft Dynamics Lifecycle Services-projektek megjelenítése |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/write | Létrehozhat és frissíthet, a felhasználóhoz tartozik Microsoft Dynamics Lifecycle Services-projektek. Csak a név és leírás tulajdonságai frissíthetők. Az előfizetésben és helyen a projekthez kapcsolódó létrehozása után nem frissíthető |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/delete | Egy eventSubscription törlése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Az esemény-előfizetés teljes URL-cím lekérése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Esemény-előfizetések diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Az esemény-előfizetésekhez diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | EventSubscriptions elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/read | Olvassa el a eventSubscription |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/write | Létrehozás vagy frissítés egy eventSubscription |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikáinak beolvasása kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/register/action | Regisztrálja a eventSubscription EventGrid erőforrás-szolgáltató, és lehetővé teszi az Event Grid-előfizetések létrehozását. |
> | Műveletek | Microsoft.EventGrid/topics/delete | A témakör törlése |
> | Műveletek | Microsoft.EventGrid/topics/listKeys/action | A témakör kulcsainak listázása |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikáinak beolvasása kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/topics/read | Olvassa el a témakör |
> | Műveletek | Microsoft.EventGrid/topics/regenerateKey/action | A témakör a kulcs újragenerálása |
> | Műveletek | Microsoft.EventGrid/topics/write | Létrehozni vagy frissíteni a témakör |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventHub/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.EventHub/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja inkább a checknameavailabiltiy API-t. |
> | Műveletek | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Fürtmetrikák erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/read | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/write | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Az API-t a put műveletet. Put művelet meghívásával használja helyette a a Namespace-engedélyezési szabály frissítése... Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/write | Egy Namespace engedélyezési szabály létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai, valamint elsődleges és másodlagos kulcsai frissíthetők. |
> | Műveletek | Microsoft.EventHub/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | A névtérhez hozzárendelt vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Az EventHub frissítési művelete. Ez a művelet nem támogatott az API 2017-04-01-es verzióját. Az engedélyezési szabályok. Engedélyezési szabály frissítéséhez használja a Put művelet meghívásával. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Az EventHub-engedélyezési szabályok törlésére szolgáló művelet |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Az EventHub kapcsolati karakterláncának lekérése |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Az EventHub-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Az EventHub-engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai frissíthetők. |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | A fogyasztói csoporttól erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | A fogyasztói csoporttól erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Hozzon létre vagy frissítés ConsumerGroup tulajdonságai. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/Delete | Az EventHub erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/read | Az EventHub-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/write | Hozzon létre vagy frissítés EventHub tulajdonságait. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/read | Beolvassa az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/write | Frissíti az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.EventHub/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Namespace naplók erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.EventHub/namespaces/write | Namespace erőforrás létrehozása és tulajdonságainak frissítése. Címkék és a kapacitás, a Namespace olyan tulajdonságai frissíthetők. |
> | Műveletek | Microsoft.EventHub/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.EventHub/register/action | Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.EventHub/sku/read | Termékváltozat-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/sku/regions/read | SkuRegions erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/unregister/action | Az EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Features/features/read | Beolvassa az előfizetéshez tartozó szolgáltatásokat. |
> | Műveletek | Microsoft.Features/operations/read | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft.Features/providers/features/read | Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/providers/features/register/action | Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/providers/features/unregister/action | Az előfizetéshez tartozó szolgáltatás regisztrációjának törlése az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/register/action | Az előfizetésben elérhető funkció regisztrálása. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight-fürt RDP-beállítás módosítása |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/action | HDInsight-fürt konfigurációjának frissítése |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/read | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/delete | Egy HDInsight-fürt törlése |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | A HDInsight-fürt erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Az a HDInsight-fürt erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | A HDInsight-fürtön elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.HDInsight/clusters/read | HDInsight-fürt részleteinek beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/roles/resize/action | A HDInsight-fürt méretezése |
> | Műveletek | Microsoft.HDInsight/clusters/write | HDInsight-fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft.HDInsight/locations/capabilities/read | Az előfizetési lehetőségek elérése |
> | Műveletek | Microsoft.HDInsight/locations/checkNameAvailability/read | Név foglaltságának ellenőrzése |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ImportExport/jobs/delete | Egy meglévő feladat törlése. |
> | Műveletek | Microsoft.ImportExport/jobs/listBitLockerKeys/action | A megadott feladathoz tartozó BitLocker-kulcsok beolvasása. |
> | Műveletek | Microsoft.ImportExport/jobs/read | A megadott feladat tulajdonságainak lekérése vagy a feladatok listáját adja vissza. |
> | Műveletek | Microsoft.ImportExport/jobs/write | Létrehoz egy feladatot a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott feladat frissítése. |
> | Műveletek | Microsoft.ImportExport/locations/read | A megadott hely tulajdonságainak lekérése vagy a helyek listáját adja vissza. |
> | Műveletek | Microsoft.ImportExport/register/action | Az importálási/exportálási erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi az importálási/exportálási feladatok létrehozását. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Insights/ActionGroups/Delete | Műveletcsoport törlése |
> | Műveletek | Microsoft.Insights/ActionGroups/Read | Műveletcsoport olvasása |
> | Műveletek | Microsoft.Insights/ActionGroups/Write | Létrehozni vagy frissíteni a műveletcsoport |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Tevékenység Log riasztás aktiválva |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Delete | Tevékenységnapló-alapú riasztás törlése |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Read | Tevékenységnapló-alapú riasztás olvasása |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Write | Tevékenységnapló-alapú riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/AlertRules/Activated/Action | Klasszikus metrikaalapú riasztás aktiválva |
> | Műveletek | Microsoft.Insights/AlertRules/Delete | Klasszikus metrikaalapú riasztás törlése |
> | Műveletek | Microsoft.Insights/AlertRules/Incidents/Read | Klasszikus metrikaalapú riasztási eset olvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Read | Klasszikus metrikaalapú riasztás olvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Resolved/Action | Klasszikus metrikaalapú riasztás megoldva |
> | Műveletek | Microsoft.Insights/AlertRules/Throttled/Action | Klasszikus metrikaalapú riasztási szabály elindítva |
> | Műveletek | Microsoft.Insights/AlertRules/Write | Klasszikus metrikaalapú riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Delete | Automatikus méretezési beállítás törlése |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Read | Automatikus méretezési beállítás olvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatikus vertikális leskálázás inicializálva |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Automatikus vertikális leskálázás befejezve |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatikus vertikális felskálázás inicializálva |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatikus vertikális felskálázás befejezve |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Write | Automatikus méretezési beállítás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Delete | Egy Application Insights-elemzési elem törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Read | Egy Application Insights-elemzési elem olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Write | Egy Application Insights-elemzési elem írása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Action | Az Application Insights-elemzési táblájának művelete |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Delete | Egy Application Insights-elemzési tábla sémájának törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Read | Egy Application Insights-elemzési tábla sémájának olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Write | Egy Application Insights-elemzési tábla sémájának írása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Delete | Egy Application Insights-jegyzet törlése |
> | Műveletek | Microsoft.Insights/Components/Annotations/Read | Egy Application Insights-jegyzet olvasása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Write | Egy Application Insights-jegyzet írása |
> | Műveletek | Microsoft.Insights/Components/Api/Read | Application Insights-összetevőadatok API-jának olvasása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Action | Egy Application Insights-beli API-kulcs létrehozása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Delete | Egy Application Insights-beli API-kulcs törlése |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Read | Egy Application Insights-beli API-kulcs olvasása |
> | Műveletek | Microsoft.Insights/Components/BillingPlanForComponent/Read | Egy Application Insights-összetevő számlázási csomagjának olvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights-összetevő aktuális számlázási funkcióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights-összetevő aktuális számlázási funkcióinak írása |
> | Műveletek | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Az Application Insights egy alapértelmezett ALM-integrációja konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/Delete | Application Insights-összetevő konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/Events/Read | Naplók beolvasása az Application Insightsból OData-lekérdezési formátumban |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Action | Az Application Insights exportálási beállításainak művelete |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Delete | Az Application Insights exportálási beállításainak törlése |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Read | Az Application Insights exportálási beállításainak olvasása |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Write | Az Application Insights exportálási beállításainak írása |
> | Műveletek | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights-összetevő bővített lekérdezési eredményeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Delete | Egy Application Insights-kedvenc törlése |
> | Műveletek | Microsoft.Insights/Components/Favorites/Read | Egy Application Insights-kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Write | Egy Application Insights-kedvenc írása |
> | Műveletek | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights-összetevő funkciói lehetőségeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights-összetevő elérhető számlázási funkcióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/GetToken/Read | Egy Application Insights-összetevő jogkivonatának olvasása |
> | Műveletek | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights-összetevő metrikadefinícióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/Metrics/Read | Application Insights-összetevő metrikáinak olvasása |
> | Műveletek | Microsoft.Insights/Components/Move/Action | Application Insights-összetevő áthelyezése másik erőforráscsoportba vagy előfizetésbe |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Egy személyes Application Insights-elemzési elem törlése |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Read | Egy személyes Application Insights-elemzési elem olvasása |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Write | Egy személyes Application Insights-elemzési elem írása |
> | Műveletek | Microsoft.Insights/Components/MyFavorites/Read | Egy személyes Application Insights-kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/Operations/Read | Az Application Insightsban hosszú ideig futó műveletek állapotának beolvasása |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Read | Egy Application Insights-összetevő tarifacsomagjának olvasása |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Write | Egy Application Insights-összetevő tarifacsomagjának írása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Az Application Insights Proaktív problémaészlelés konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Az Application Insights Proaktív problémaészlelés konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/Components/Purge/Action | Adatok végleges törlése az Application Insightsból |
> | Műveletek | Microsoft.Insights/Components/Query/Read | Lekérdezések futtatása Application Insights-naplókon |
> | Műveletek | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights-összetevő kvótaállapotának olvasása |
> | Műveletek | Microsoft.Insights/Components/Read | Application Insights-összetevő konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights-webtesztek helyének olvasása |
> | Műveletek | Microsoft.Insights/Components/Webtests/Read | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Delete | Az Application Insights egy ALM integrációja konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Read | Az Application Insights egy ALM-integrációja konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Write | Az Application Insights egy ALM-integrációja konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/Write | Application Insights-összetevő konfigurációjának írása |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Delete | Erőforrás diagnosztikai beállításának törlése |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Read | Erőforrás diagnosztikai beállításának olvasása |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Write | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/EventCategories/Read | Tevékenységnaplóbeli eseménykategóriák olvasása |
> | Műveletek | Microsoft.Insights/eventtypes/digestevents/Read | A felügyeleti eseménytípus kivonatának olvasása |
> | Műveletek | Microsoft.Insights/eventtypes/values/Read | Tevékenységnaplóbeli események olvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Hálózati forgalomfolyam-napló diagnosztikai beállításának törlése |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Hálózati forgalomfolyam-napló diagnosztikai beállításának olvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Hálózati forgalomfolyam-napló diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Action | Előfizetés migrálási dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Read | Előfizetés migrálási dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/LogDefinitions/Read | A naplódefiníciók olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Delete | Tevékenységnapló naplóprofiljának törlése |
> | Műveletek | Microsoft.Insights/LogProfiles/Read | Tevékenységnapló naplóprofiljának olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Write | Tevékenységnapló naplóprofiljának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/MetricAlerts/Delete | Metrikaalapú riasztás törlése |
> | Műveletek | Microsoft.Insights/MetricAlerts/Read | Metrikaalapú riasztás olvasása |
> | Műveletek | Microsoft.Insights/MetricAlerts/Status/Read | Metrikaalapú riasztás állapotának olvasása |
> | Műveletek | Microsoft.Insights/MetricAlerts/Write | Metrikaalapú riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/Metrics/providers/Metrics/Read | Metrikák olvasása |
> | Műveletek | Microsoft.Insights/Metrics/Read | Metrikák olvasása |
> | DataAction | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | Műveletek | Microsoft.Insights/MigrateToNewpricingModel/Action | Előfizetés migrálása új díjszabási modellbe |
> | Műveletek | Microsoft.Insights/Operations/Read | Olvasási műveletek |
> | Műveletek | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Műveletek | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Előfizetés visszaállítása a régi díjszabási modellre |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Delete | Ütemezett lekérdezés szabály törlése |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Read | Egy ütemezett lekérdezési szabály olvasása |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Write | Ütemezett lekérdezési szabály írása |
> | Műveletek | Microsoft.Insights/Tenants/Register/Action | A Microsoft Insights-szolgáltató inicializálása |
> | Műveletek | Microsoft.Insights/Unregister/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Műveletek | Microsoft.Insights/Webtests/Delete | Webteszt-konfiguráció törlése |
> | Műveletek | Microsoft.Insights/Webtests/GetToken/Read | Egy webteszt jogkivonatának olvasása |
> | Műveletek | Microsoft.Insights/Webtests/MetricDefinitions/Read | Egy webteszt metrikadefinícióinak olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Metrics/Read | Egy webteszt metrikáinak olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Read | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Write | Webteszt-konfiguráció írása |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.KeyVault/checkNameAvailability/read | Annak ellenőrzése, hogy érvényes-e a kulcstartónév, és nincs-e használatban |
> | Műveletek | Microsoft.KeyVault/deletedVaults/read | Helyreállíthatóan törölt kulcstartók tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/delete | HSM-készlet törlése |
> | Műveletek | Microsoft.KeyVault/hsmPools/joinVault/action | Kulcstartó HSM-készlethez való csatlakoztatása |
> | Műveletek | Microsoft.KeyVault/hsmPools/read | A HSM-készlet tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/write | Új HSM-készlet létrehozása vagy egy meglévő HSM-készlet tulajdonságainak frissítése |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/read | Helyreállíthatóan törölt kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.KeyVault értesítése arról, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.KeyVault/locations/operationResults/read | Hosszú futtatású művelet eredményének ellenőrzése |
> | Műveletek | Microsoft.KeyVault/operations/read | A Microsoft.KeyVault erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.KeyVault/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.KeyVault/unregister/action | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.KeyVault/vaults/accessPolicies/write | Egyesítéssel vagy cserével frissíthet egy meglévő hozzáférési szabályzatot, vagy létrehozhat egy új hozzáférési szabályzatot a tárolóban. |
> | Műveletek | Microsoft.KeyVault/vaults/delete | Kulcstároló törlése |
> | Műveletek | Microsoft.KeyVault/vaults/deploy/action | A kulcstartóban található titkos kódokhoz való hozzáférés engedélyezése Azure-erőforrások üzembe helyezésekor |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a kulcstartóhoz elérhető naplókat |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a kulcstartóhoz elérhető metrikákat |
> | Műveletek | Microsoft.KeyVault/vaults/read | A kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/read | A titkos kód tulajdonságainak megtekintése, kivéve a kód értékét |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/write | Új titkos kód létrehozása, vagy meglévő titkos kód értékének módosítása |
> | Műveletek | Microsoft.KeyVault/vaults/write | Új kulcstartó létrehozása, vagy meglévő kulcstartó tulajdonságainak módosítása |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/delete | Egy adatbázis-erőforrás törlése. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Egy event hub-kapcsolatok erőforrás törlése. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Egy event hub-kapcsolatok erőforrás beolvasása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Az event hub connetions-erőforrás írása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/read | Egy adatbázis-erőforrás beolvasása. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/write | Egy adatbázis-erőforrás írása. |
> | Műveletek | Microsoft.Kusto/Clusters/delete | A fürterőforrás törlése. |
> | Műveletek | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításait |
> | Műveletek | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/metricDefinitions/read | Az erőforrás metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Kusto/Clusters/read | A fürterőforrás beolvasása. |
> | Műveletek | Microsoft.Kusto/Clusters/write | A fürterőforrás ír. |
> | Műveletek | Microsoft.Kusto/Locations/CheckNameAvailability/write | Olvasási ellenőrizze a rendelkezésre állási erőforrás neve |
> | Műveletek | Microsoft.Kusto/locations/operationresults/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.Kusto/Operations/read | Olvasási műveletek erőforrások |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LabServices/labAccounts/CreateLab/action | Labor létrehozása a lab-fiókokban. |
> | Műveletek | Microsoft.LabServices/labAccounts/delete | Törölje a lab-fiókokban. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/delete | Image z galerie törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/read | Olvassa el a katalógus rendszerképeinek letöltéséhez. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/write | Adja hozzá, vagy módosítsa a katalógus rendszerképeinek letöltéséhez. |
> | Műveletek | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Tesztkörnyezetfiók konfigurált mérete kategóriákhoz tartozó régiónkénti rendelkezésre állás információk lekérése |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/delete | Törölje a labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Egy véletlenszerű környezetet egy felhasználó egy környezeti beállítások a jogcímeket |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | A környezet jogcímek, és hozzárendeli a felhasználóhoz |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Környezetek olvasása. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Elindítja egy környezetet a környezeten belül minden erőforrás elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | A környezeten belül minden erőforrás leállításával leállítja a környezet |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Rendelkezések/deprovisions szükséges egy beállítás környezet erőforrásait a laborkörnyezetben/beállítás aktuális állapota alapján. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Egy sablon elindul a sablonban található összes erőforrást elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Egy sablon elindul a sablonban található összes erőforrást elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezetben a beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/Register/action | Regisztrálja a felügyelt laborhoz. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/delete | Felhasználók törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/read | Olvassa el a felhasználókat. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/write | Hozzáadása vagy módosítása a felhasználók. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/write | Hozzáadása vagy módosítása a labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/read | Olvassa el a lab-fiókokban. |
> | Műveletek | Microsoft.LabServices/labAccounts/sizes/GetRegionalAvailability/action | Tesztkörnyezetfiók konfigurált mérete kategóriákhoz tartozó régiónkénti rendelkezésre állás információk lekérése |
> | Műveletek | Microsoft.LabServices/labAccounts/sizes/read | Olvassa el a méreteket. |
> | Műveletek | Microsoft.LabServices/labAccounts/sizes/ResetOverrides/action | Alaphelyzetbe állítja a méretre vonatkozó konfigurációja a labor fiók alapértékekre |
> | Műveletek | Microsoft.LabServices/labAccounts/write | Hozzáadása vagy módosítása a lab-fiókokban. |
> | Műveletek | Microsoft.LabServices/locations/operations/read | Olvasási műveletek. |
> | Műveletek | Microsoft.LabServices/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.LabServices/users/GetEnvironment/action | Virtuális gép részleteinek beolvasása |
> | Műveletek | Microsoft.LabServices/users/GetOperationBatchStatus/action | Kötegelt műveleti állapotának beolvasása |
> | Műveletek | Microsoft.LabServices/users/GetOperationStatus/action | A hosszabb ideig futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.LabServices/users/ListEnvironments/action | A felhasználó Platformjából |
> | Műveletek | Microsoft.LabServices/users/ListLabs/action | A felhasználó labs listája. |
> | Műveletek | Microsoft.LabServices/users/Register/action | Egy felhasználó egy felügyelt laborhoz regisztrálása |
> | Műveletek | Microsoft.LabServices/users/StartEnvironment/action | Elindítja egy környezetet a környezeten belül minden erőforrás elindításával. |
> | Műveletek | Microsoft.LabServices/users/StopEnvironment/action | A környezeten belül minden erőforrás leállításával leállítja a környezet |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LocationBasedServices/accounts/delete | (Elavult: /providers/Microsoft.Maps használja) Törölje a Location Based Services-fiók. |
> | Műveletek | Microsoft.LocationBasedServices/accounts/listKeys/action | (Elavult: /providers/Microsoft.Maps használja) Location Based Services-fiók kulcsainak listázása |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Elavult: /providers/Microsoft.Maps használja) Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Elavult: /providers/Microsoft.Maps használja) A az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Elavult: /providers/Microsoft.Maps használja) Hely Based Services-fiókok számára elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.LocationBasedServices/accounts/read | (Elavult: /providers/Microsoft.Maps használja) Get-Location Based Services-fiók. |
> | Műveletek | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Elavult: /providers/Microsoft.Maps használja) Hozzon létre új Location Based Services-fiók elsődleges vagy másodlagos kulcsot |
> | Műveletek | Microsoft.LocationBasedServices/accounts/write | (Elavult: /providers/Microsoft.Maps használja) Hozzon létre vagy nem frissíthető a Location Based Services fiók. |
> | Műveletek | Microsoft.LocationBasedServices/register/action | (Elavult: /providers/Microsoft.Maps használja) A szolgáltató regisztrálása |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LocationServices/accounts/delete | (Elavult: /providers/Microsoft.Maps használja) Hely-Services-fiók törlése. |
> | Műveletek | Microsoft.LocationServices/accounts/listKeys/action | (Elavult: /providers/Microsoft.Maps használja) Location Based Services-fiók kulcsainak listázása |
> | Műveletek | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Elavult: /providers/Microsoft.Maps használja) Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Elavult: /providers/Microsoft.Maps használja) A az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Elavult: /providers/Microsoft.Maps használja) Hely Based Services-fiókok számára elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.LocationServices/accounts/read | (Elavult: /providers/Microsoft.Maps használja) Hely-Services-fiók beolvasása. |
> | Műveletek | Microsoft.LocationServices/accounts/regenerateKey/action | (Elavult: /providers/Microsoft.Maps használja) Hozzon létre új Location Based Services-fiók elsődleges vagy másodlagos kulcsot |
> | Műveletek | Microsoft.LocationServices/accounts/write | (Elavult: /providers/Microsoft.Maps használja) Létrehozása vagy frissítése egy Location Services-fiók. |
> | Műveletek | Microsoft.LocationServices/register/action | (Elavult: /providers/Microsoft.Maps használja) A szolgáltató regisztrálása |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Adatok olvasása a ADAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Adatok olvasása a ADReplicationResult táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Adatok olvasása a ADSecurityAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | A riasztási táblából adatokat olvasni. |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Adatok olvasása a AlertHistory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Adatok olvasása a ApplicationInsights táblából |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Adatok olvasása a AzureActivity táblából |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Adatok olvasása a AzureMetrics táblából |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Adatok olvasása a BoundPort táblából |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Adatok olvasása a CommonSecurityLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Adatok olvasása a ComputerGroup táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | A konfigurációváltozás táblából adatokat olvasni. |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Adatok olvasása a ConfigurationData táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Adatok olvasása a ContainerImageInventory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Adatok olvasása a ContainerInventory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Adatok olvasása a ContainerLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Adatok olvasása a ContainerServiceLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/CustomLogs/read | Bármilyen egyéni naplóból származó adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Adatok olvasása a DeviceAppCrash táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Adatok olvasása a DeviceAppLaunch táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Adatok olvasása a DeviceCalendar táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Adatok olvasása a DeviceCleanup táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Adatok olvasása a DeviceConnectSession táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Adatok olvasása a DeviceEtw táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Adatok olvasása a DeviceHardwareHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Adatok olvasása a DeviceHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Adatok olvasása a DeviceHeartbeat táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Adatok olvasása a DeviceSkypeHeartbeat táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Adatok olvasása a DeviceSkypeSignIn táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Adatok olvasása a DeviceSleepState táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Adatok olvasása a DHAppFailure táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Adatok olvasása a DHAppReliability táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Adatok olvasása a DHDriverReliability táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Adatok olvasása a DHLogonFailures táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Adatok olvasása a DHLogonMetrics táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Adatok olvasása a DHOSCrashData táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Adatok olvasása a DHOSReliability táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Adatok olvasása a DHWipAppLearning táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Adatok olvasása a DnsEvents táblából |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Adatok olvasása a DnsInventory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Adatok olvasása a ETWEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Adatokat olvasni az esemény-tábla |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Adatok olvasása a ExchangeAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Adatok olvasása a ExchangeOnlineAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Adatok olvasása a szívverés-táblából |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Adatok olvasása a IISAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Adatok olvasása a InboundConnection táblából |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Adatok olvasása a KubeNodeInventory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Adatok olvasása a KubePodInventory táblából |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Adatok olvasása a LinuxAuditLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Adatok olvasása a MAApplication táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Adatok olvasása a MAApplicationHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Adatok olvasása a MAApplicationHealthAlternativeVersions táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Adatok olvasása a MAApplicationHealthIssues táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Adatok olvasása a MAApplicationInstance táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Adatok olvasása a MAApplicationInstanceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Adatok olvasása a MAApplicationReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Adatok olvasása a MADeploymentPlan táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Adatok olvasása a MADevice táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Adatok olvasása a MADevicePnPHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Adatok olvasása a MADevicePnPHealthAlternativeVersions táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Adatok olvasása a MADevicePnPHealthIssues táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Adatok olvasása a MADeviceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Adatok olvasása a MADriverInstanceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Adatok olvasása a MADriverReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Adatok olvasása a MAOfficeAddin táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Adatok olvasása a MAOfficeAddinHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Adatok olvasása a MAOfficeAddinHealthIssues táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Adatok olvasása a MAOfficeAddinInstance táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Adatok olvasása a MAOfficeAddinInstanceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Adatok olvasása a MAOfficeAddinReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Adatok olvasása a MAOfficeApp táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Adatok olvasása a MAOfficeAppHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Adatok olvasása a MAOfficeAppInstance táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Adatok olvasása a MAOfficeAppReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Adatok olvasása a MAOfficeBuildInfo táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Adatok olvasása a MAOfficeCurrencyAssessment táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Adatok olvasása a MAOfficeCurrencyAssessmentDailyCounts táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Adatok olvasása a MAOfficeDeploymentStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Adatok olvasása a MAOfficeMacroHealth táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Adatok olvasása a MAOfficeMacroHealthIssues táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Adatok olvasása a MAOfficeMacroIssueInstanceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Adatok olvasása a MAOfficeMacroIssueReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Adatok olvasása a MAOfficeMacroSummary táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Adatok olvasása a MAOfficeSuite táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Adatok olvasása a MAOfficeSuiteInstance táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Adatok olvasása a MAProposedPilotDevices táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Adatok olvasása a MAWindowsBuildInfo táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Adatok olvasása a MAWindowsCurrencyAssessment táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Adatok olvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Adatok olvasása a MAWindowsDeploymentStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Adatok olvasása a MAWindowsSysReqInstanceReadiness táblából |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Adatok olvasása a NetworkMonitoring táblából |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Adatok olvasása a OfficeActivity táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | A művelettel tábla adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Adatok olvasása a OutboundConnection táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Adatok olvasása a teljesítményadat-táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Adatok olvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft.LogAnalytics/logs/read | Adatok olvasása a naplókból |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Adatok olvasása a ReservedAzureCommonFields táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Adatok olvasása a ReservedCommonFields táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Adatok olvasása a SCCMAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Adatok olvasása a SCOMAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Adatok olvasása a SecurityAlert táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Adatok olvasása a SecurityBaseline táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Adatok olvasása a SecurityBaselineSummary táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Adatok olvasása a SecurityDetection táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Adatok olvasása a SecurityEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Adatok olvasása a ServiceFabricOperationalEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Adatok olvasása a ServiceFabricReliableActorEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Adatok olvasása a ServiceFabricReliableServiceEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Adatok olvasása a SfBAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Adatok olvasása a SfBOnlineAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Adatok olvasása a SharePointOnlineAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Adatok olvasása a SPAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Adatok olvasása a SQLAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Adatok olvasása a SQLQueryPerformance táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Adatok olvasása a Syslog-táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Adatok olvasása a SysmonEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Adatok olvasása a UAApp táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Adatok olvasása a UAComputer táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Adatok olvasása a UAComputerRank táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Adatok olvasása a UADriver táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Adatok olvasása a UADriverProblemCodes táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Adatok olvasása a UAFeedback táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Adatok olvasása a UAHardwareSecurity táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Adatok olvasása a UAIESiteDiscovery táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Adatok olvasása a UAOfficeAddIn táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Adatok olvasása a UAProposedActionPlan táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Adatok olvasása a UASysReqIssue táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Adatok olvasása a UAUpgradedComputer táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Adatok beolvasása a tábla frissítése |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Adatok olvasása a UpdateRunProgress táblából |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Adatok olvasása a UpdateSummary táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Adatok beolvasása a használati táblához |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Adatok olvasása a VMBoundPort táblából |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Adatok olvasása a VMConnection táblából |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Adatok olvasása a W3CIISLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Adatok olvasása a WaaSDeploymentStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Adatok olvasása a WaaSInsiderStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Adatok olvasása a WaaSUpdateStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Adatok olvasása a WDAVStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Adatok olvasása a WDAVThreat táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Adatok olvasása a WindowsClientAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Adatok olvasása a WindowsFirewall táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Adatok olvasása a WindowsServerAssessmentRecommendation táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Adatok olvasása a WireData-táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Adatok olvasása a WUDOAggregatedStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Adatok olvasása a WUDOStatus táblából |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/delete | Egy integrációs fiókban lévő megállapodás törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Egy integrációs fiókban lévő megállapodástartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/read | Egy integrációs fiókban lévő megállapodás beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/write | A megállapodás létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/delete | Egy integrációs fiókban lévő szerelvény törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Egy integrációs fiókban lévő szerelvénytartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/read | Egy integrációs fiókban lévő szerelvény beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/write | A szerelvény létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Egy integrációs fiókban lévő kötegkonfiguráció törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/write | A kötegkonfiguráció létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/delete | Egy integrációs fiókban lévő tanúsítvány törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/read | Egy integrációs fiókban lévő tanúsítvány beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/write | A tanúsítvány létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/delete | Egy integrációs fiók törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/join/action | Az integrációs fiók csatlakoztatása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Egy integrációs fiók visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Egy kulcstartóban lévő kulcsok beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Egy integrációs fiókban bekövetkező követési események naplózása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/delete | Egy integrációs fiókban lévő leképezés törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Egy integrációs fiókban lévő hozzárendelési tartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/read | Egy integrációs fiókban lévő hozzárendelés beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/write | A hozzárendelés létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/delete | Egy integrációs fiókban lévő partner törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Egy integrációs fiókban lévő partnertartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/read | Egy integrációs fiókban lévő partner beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/write | A partner létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Az integrációs fiók naplódefinícióinak olvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/read | Egy integrációs fiók beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/delete | Egy integrációs fiókban lévő séma törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Egy integrációs fiókban lévő sématartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/read | Egy integrációs fiókban lévő séma beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/write | A séma létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/delete | Egy integrációs fiókban lévő munkamenet törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/write | A munkamenet létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/write | Egy integrációs fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/delete | Törli az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/join/action | Csatlakozik az integrációs szolgáltatási környezethez. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/read | Beolvassa az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/write | Az integrációs szolgáltatási környezet létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Logic/locations/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/operations/read | Egy művelet beolvasása. |
> | Műveletek | Microsoft.Logic/register/action | A Microsoft.Logic erőforrás-szolgáltató regisztrálása egy adott előfizetéshez. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/delete | Törli a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/list/action | Listázza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/read | Beolvassa a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/regenerate/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/write | Létrehozza vagy frissíti a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/delete | Törli a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/listCallbackUrl/action | Egy munkafolyamat visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/listSwagger/action | A munkafolyamat Swagger-definícióinak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/move/action | A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Beolvassa a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a munkafolyamat naplómeghatározásait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a munkafolyamat metrikai meghatározásait. |
> | Műveletek | Microsoft.Logic/workflows/read | Beolvassa a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/run/action | Elindítja egy munkafolyamat futtatását. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | A munkafolyamat-futtatási műveleti kifejezés követési adatainak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/read | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Egy munkafolyamat-futtatási művelet ismétlődési kifejezéskövetési adatainak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/read | Egy munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | A munkafolyamat-futtatási ismétlődéskérelem-előzmények olvasása |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/requestHistories/read | A munkafolyamat-futtatási műveletkérelem-előzmények olvasása |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Egy munkafolyamat-futtatási művelet hatóköre ismétlődésének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/cancel/action | Megszakítja egy munkafolyamat futtatását. |
> | Műveletek | Microsoft.Logic/workflows/runs/operations/read | Beolvassa a munkafolyamat-futtatás műveleti állapotát. |
> | Műveletek | Microsoft.Logic/workflows/runs/read | Beolvassa a munkafolyamat-futtatást. |
> | Műveletek | Microsoft.Logic/workflows/suspend/action | Egy munkafolyamat felfüggesztése. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/read | Beolvassa a triggerelőzményeket. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/resubmit/action | A munkafolyamat-trigger újraküldése. |
> | Műveletek | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/triggers/read | Beolvassa a triggert. |
> | Műveletek | Microsoft.Logic/workflows/triggers/reset/action | A trigger alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Logic/workflows/triggers/run/action | Végrehajtja a triggert. |
> | Műveletek | Microsoft.Logic/workflows/triggers/setState/action | A trigger állapotának beállítása. |
> | Műveletek | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/versions/read | Beolvassa a munkafolyamat verzióját. |
> | Műveletek | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/write | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Helyezze át minden olyan gépi tanulási a kötelezettségvállalási csomag társítása |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Olvassa el minden olyan gépi tanulási a kötelezettségvállalási csomag társítása |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/delete | Bármely Machine Learning kötelezettségvállalási csomag törlése |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/join/action | Csatlakozás bármely Machine Learning kötelezettségvállalási csomag |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/read | Olvassa el a bármely Machine Learning kötelezettségvállalási csomag |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/write | Hozzon létre vagy bármely Machine Learning kötelezettségvállalási csomag frissítése |
> | Műveletek | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning művelet eredményének beolvasása |
> | Műveletek | Microsoft.MachineLearning/locations/operationsstatus/read | Machine Learning folyamatban lévő művelet állapotának beolvasása |
> | Műveletek | Microsoft.MachineLearning/operations/read | Machine Learning-műveletek lekérése |
> | Műveletek | Microsoft.MachineLearning/register/action | A machine learning web service erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a webes szolgáltatások létrehozását. |
> | Műveletek | Microsoft.MachineLearning/skus/read | A Machine Learning kötelezettségvállalási csomag termékváltozatok beolvasása |
> | Műveletek | Microsoft.MachineLearning/webServices/action | Támogatott régiók regionális webes szolgáltatás tulajdonságok létrehozása |
> | Műveletek | Microsoft.MachineLearning/webServices/delete | Bármely Machine Learning-webszolgáltatás törlése |
> | Műveletek | Microsoft.MachineLearning/webServices/listkeys/read | A Machine Learning Web Service-kulcsok beolvasása |
> | Műveletek | Microsoft.MachineLearning/webServices/read | Olvassa el a bármely Machine Learning webszolgáltatás |
> | Műveletek | Microsoft.MachineLearning/webServices/write | Létrehozásához vagy frissítéséhez bármely Machine Learning webszolgáltatás |
> | Műveletek | Microsoft.MachineLearning/Workspaces/delete | Bármely Machine Learning-munkaterület törlése |
> | Műveletek | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | A Machine Learning-munkaterület kulcsainak listázása |
> | Műveletek | Microsoft.MachineLearning/Workspaces/read | Olvassa el a bármely Machine Learning-munkaterület |
> | Műveletek | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | A Machine Learning-munkaterület beállított storage-fiók kulcsok újraszinkronizálása |
> | Műveletek | Microsoft.MachineLearning/Workspaces/write | Hozzon létre vagy bármely Machine Learning-munkaterület frissítése |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Ellenőrizze, hogy ha frissítések érhetők el a operacionalizálás fürthöz-szolgáltatások |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Bármely szolgáltatási fiók törlése |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Operacionalizálás fürthöz társított kulcsainak listázása |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/read | Bármely szolgáltatási fiók beolvasása |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Az operacionalizálás fürthöz a rendszerszolgáltatások frissítése |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/write | Hozzon létre vagy bármely szolgáltatási fiók frissítése |
> | Műveletek | Microsoft.MachineLearningCompute/register/action | Előfizetés-azonosító, az erőforrás-szolgáltató regisztrálása és a egy Machine learning számítási erőforrások létrehozásának engedélyezése |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/delete | Bármely szolgáltatási fiók törlése |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/read | Bármely szolgáltatási fiók beolvasása |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/write | Hozzon létre vagy bármely szolgáltatási fiók frissítése |
> | Műveletek | Microsoft.MachineLearningModelManagement/register/action | Előfizetés-azonosító, az erőforrás-szolgáltató regisztrálása és a szolgáltatási fiók létrehozásának engedélyezése |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningServices/register/action | A Machine Learning szolgáltatások erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services már a számítási erőforrások törlése |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | A Machine Learning-munkaterület számítási erőforrások titkos kódjainak listázására |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/read | Machine Learning Services már a számítási erőforrások beolvasása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/write | Létrehozza vagy frissíti a Machine Learning Services már a számítási erőforrások |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/delete | A Machine Learning-szolgáltatások munkaterületeire törlése |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/listKeys/action | A Machine Learning-szolgáltatások munkaterület titkos kulcsainak listázása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/read | A Machine Learning-szolgáltatások munkaterületeire beolvasása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/write | Létrehozza vagy frissíti a Machine Learning Services munkaterületeire |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC művelet hozzárendelése egy meglévő felhasználó identitása van hozzárendelve egy erőforráshoz |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Töröl egy meglévő felhasználóhoz hozzárendelt identitás |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/read | Lekérdezi egy meglévő felhasználóhoz hozzárendelt identitás |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/write | Egy új felhasználóhoz hozzárendelt identitás létrehozása vagy frissítése egy meglévő felhasználóhoz hozzárendelt identitás társított címkék |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedLab/labAccounts/CreateLab/action | Labor létrehozása a lab-fiókokban. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/delete | Törölje a lab-fiókokban. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/delete | Törölje a labs. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Környezetek olvasása. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezetben a beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Törölje a virtuális gépeiről. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Olvassa el a virtuális gépeiről. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Adja hozzá, vagy módosítsa a virtuális gépeiről. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/write | Hozzáadása vagy módosítása a labs. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/read | Olvassa el a lab-fiókokban. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/write | Hozzáadása vagy módosítása a lab-fiókokban. |
> | Műveletek | Microsoft.ManagedLab/locations/operations/read | Olvasási műveletek. |
> | Műveletek | Microsoft.ManagedLab/register/action | Az előfizetés regisztrálása |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Management/checkNameAvailability/action | Ellenőrzi, hogy a megadott felügyeleti csoport neve érvényes, egyedi. |
> | Műveletek | Microsoft.Management/getEntities/action | Listázza az összes entitás (felügyeleti csoportokhoz, előfizetésekhez, stb.) a hitelesített felhasználó számára. |
> | Műveletek | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Műveletek | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportok listája. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/delete | A felügyeleti csoportból előfizetés megszüntetéséhez társítja. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/write | Hozzárendeli a meglévő előfizetés a felügyeleti csoporttal. |
> | Műveletek | Microsoft.Management/managementGroups/write | Hozzon létre, vagy a felügyeleti csoport frissítése. |
> | Műveletek | Microsoft.Management/register/action | A megadott előfizetés regisztrálása a Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Maps/accounts/delete | A Maps-fiók törlése. |
> | Műveletek | Microsoft.Maps/accounts/listKeys/action | Maps-fiók kulcsainak listázása |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Maps-fiókok elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Maps/accounts/read | A Maps-fiók beolvasása. |
> | Műveletek | Microsoft.Maps/accounts/regenerateKey/action | Hozzon létre új Maps-fiók elsődleges vagy másodlagos kulcsot |
> | Műveletek | Microsoft.Maps/accounts/write | Hozzon létre vagy nem frissíthető a Maps-fiók. |
> | Műveletek | Microsoft.Maps/register/action | A szolgáltató regisztrálása |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Adja vissza egy szerződést. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Elfogadja az aláírt szerződés. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importálja egy rendszerképet az ACR-be a felhasználó. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Egy konfigurációs adja vissza. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | A konfiguráció mentése. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/delete | Klasszikus fejlesztői szolgáltatás erőforrás-törlési művelet nem. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Klasszikus fejlesztői szolgáltatás resource management kulcsok beolvasása. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Klasszikus fejlesztői szolgáltatás beolvassa az egyszeri bejelentkezési URL-címe. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/read | Klasszikus fejlesztői szolgáltatás egy GET műveletet hajtja végre. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Egy klasszikus fejlesztői szolgáltatás resource management-kulcsokat hoz létre. |
> | Műveletek | Microsoft.MarketplaceApps/Operations/read | Olvassa el a műveletek esetében minden erőforrástípus. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Egy adott Piactéri elem szerződés megszakítása |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Egy szerződést az adott Piactéri elem visszaadása |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | A megadott Piactéri elem egy szerződést kötnek |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/read | Vissza csoportban minden szerződés a megadott előfizetéshez |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Egy adott virtuális gép Piactéri megállapodás beolvasása |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Jelentkezzen, vagy egy adott virtuális gép Piactéri szerződés megszakítása |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Media/locations/checkNameAvailability/action | Ellenőrzi, hogy a Media Services-fiók neve érhető el |
> | Műveletek | Microsoft.Media/mediaservices/assets/delete | Bármely eszköz törlése |
> | Műveletek | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Az Eszközintelligencia-titkosítási kulcs beszerzése |
> | Műveletek | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista eszköz tároló SAS URL-címek |
> | Műveletek | Microsoft.Media/mediaservices/assets/read | Bármely eszköz beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/assets/write | Létrehozás vagy frissítés, bármely eszköz |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Minden tartalom kulcs szabályzat törlése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Titkos kulcsok házirend tulajdonságainak lekérése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/read | Minden tartalom kulcs szabályzat megtekintése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/write | Hozzon létre vagy bármilyen tartalom kulcs szabályzat frissítése |
> | Műveletek | Microsoft.Media/mediaservices/delete | Minden olyan Media Services-fiók törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEventOperations/read | Minden olyan élő esemény művelet olvasása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/delete | Minden olyan élő esemény törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Élő kimenetet törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Olvassa el az élő kimenetet |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Létrehozás vagy frissítés élő kimenetet |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/read | Olvassa el az összes élő esemény |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/reset/action | Minden olyan élő esemény művelet visszaállítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/start/action | Minden olyan élő esemény indítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/stop/action | Minden olyan élő esemény művelet leállítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/write | Létrehozási vagy frissítési bármely élő esemény |
> | Műveletek | Microsoft.Media/mediaservices/liveOutputOperations/read | Minden olyan élő kimeneti művelet olvasása |
> | Műveletek | Microsoft.Media/mediaservices/read | Olvassa el az összes Media Services-fiók |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Minden Streamelési végpontot művelet olvasása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/delete | Minden Streamelési végpont törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/metricDefinitions/read | A Media Services Streaming Endpoint metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/read | Olvassa el a bármely Streamvégpont |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Minden Streamelési végpontot művelet méretezése |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Minden Streamelési végpontot művelet indítása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Minden Streamelési végpontot művelet leállítása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/write | Hozzon létre vagy bármely Streamvégpont frissítéséhez |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/delete | Minden Streamelési Lokátort törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Tartalom kulcsainak listázása |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista elérési utak |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/read | Minden Streamelési Lokátort olvasása |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/write | Létrehozás vagy frissítés minden Streamelési Lokátort. |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/delete | Minden olyan adatfolyam-továbbítási szabályzat törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/read | Minden olyan adatfolyam-továbbítási szabályzat megtekintése |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/write | Hozzon létre vagy bármely adatfolyam-továbbítási szabályzat frissítése |
> | Műveletek | Microsoft.Media/mediaservices/syncStorageKeys/action | Csatlakoztatott Azure-tárfiók tárolási Hívóbetűinek szinkronizálása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/delete | Minden átalakítási törlése |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Feladat megszakítása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/delete | Minden olyan feladat törlése |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/read | Minden olyan feladat olvasása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/write | Hozzon létre vagy bármilyen feladat frissítése |
> | Műveletek | Microsoft.Media/mediaservices/transforms/read | Minden átalakítási olvasása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/write | Létrehozás vagy frissítés bármely átalakító |
> | Műveletek | Microsoft.Media/mediaservices/write | Hozzon létre vagy bármely Media Services-fiók frissítése |
> | Műveletek | Microsoft.Media/operations/read | Használható műveletek beolvasása |
> | Műveletek | Microsoft.Media/register/action | A Media Services erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi a Media Services-fiókok létrehozása |
> | Műveletek | Microsoft.Media/unregister/action | A Media Services erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Migrate/locations/assessmentOptions/read | Az adott helyen elérhető értékelési beállítások beolvasása |
> | Műveletek | Microsoft.Migrate/locations/checknameavailability/action | Az erőforrásnév az adott előfizetésben és az adott helyen való elérhetőségének ellenőrzése |
> | Műveletek | Microsoft.Migrate/Operations/read | A Microsoft.Migrate erőforrás-szolgáltatónál elérhető műveletek listázása |
> | Műveletek | Microsoft.Migrate/projects/assessments/read | Projekt értékeléseinek listázása |
> | Műveletek | Microsoft.Migrate/projects/delete | A projekt törlése |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Értékelt gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/delete | Értékelés törlése |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Egy értékelési jelentés URL-címének letöltése |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/read | Az értékelés tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/write | Új értékelés létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Migrate/projects/groups/delete | Egy csoport törlése |
> | Műveletek | Microsoft.Migrate/projects/groups/read | Csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/groups/write | Új csoport létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Migrate/projects/keys/action | A projekt megosztott kulcsainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/machines/read | Gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/read | Projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Migrate/projects/write | Új projekt létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Migrate/register/action | Az előfizetés regisztrálása a Microsoft.Migrate erőforrás-szolgáltatónál |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NetApp/locations/operationresults/read | Egy művelet eredményének erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/locations/read | Egy rendelkezésre állási olvasási ellenőrizze az erőforrás. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Egy készlet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/read | Egy készlet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/write | Egy készlet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/metricDefinitions/read | Egy készlet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/read | Egy készleterőforráshoz olvassa be. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/delete | Egy kötet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/delete | Töröl egy csatlakoztatási cél-erőforrásra. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/read | Csatlakoztatási cél erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/write | Ír egy csatlakoztatási cél-erőforrásra. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/read | Egy kötet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/write | Egy kötet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/metricDefinitions/read | Egy kötet erőforrás törlése. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/read | Egy kötet erőforrás beolvasása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/delete | Törli a pillanatkép-erőforrást. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/read | Beolvassa a pillanatkép-erőforrás. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/write | A pillanatkép-erőforrás írása. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/write | Egy kötet erőforrás ír. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/write | Egy készlet erőforrás ír. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/delete | A tárfiók típusú erőforrást törli. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/read | Olvassa be a tárfiók típusú erőforrást. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/write | Ír egy tárfiók típusú erőforrást. |
> | Műveletek | Microsoft.NetApp/Operations/read | Egy művelet erőforrások beolvasása. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl előre meghatározott házirend |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway érhető el Ssl-beállításokat |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Az Application Gateway Waf elérhető beállítására beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy application gateway háttércímkészlet csatlakozik |
> | Műveletek | Microsoft.Network/applicationGateways/backendhealth/action | Egy application gateway háttérkiszolgáló állapotadatainak beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/delete | Egy application gateway törlése |
> | Műveletek | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Szerezze be az útvonaltáblát az Application Gateway konfigurálása |
> | Műveletek | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Szerezze be az útvonaltáblát az Application Gateway konfigurálása |
> | Műveletek | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Események lekérése az Application Gateway számára |
> | Műveletek | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Az Application Gateway számára elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Network/applicationGateways/read | Egy application gateway beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Csoportok Application Gateway Security Center-konfigurációja |
> | Műveletek | Microsoft.Network/applicationGateways/start/action | Egy application gateway elindul |
> | Műveletek | Microsoft.Network/applicationGateways/stop/action | Egy application gateway leállítja |
> | Műveletek | Microsoft.Network/applicationGateways/write | Egy application gateway létrehozása vagy frissítése egy application gateway |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/delete | Egy alkalmazás biztonsági csoport törlése |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Az alkalmazásbiztonsági csoportok csatlakoztatja egy IP-konfigurációt. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Biztonsági szabály csatlakoztatja az alkalmazásbiztonsági csoportok. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/read | Lekérdezi egy alkalmazásbiztonsági csoportot. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/write | Egy alkalmazásbiztonsági csoportot hoz létre, vagy egy meglévő alkalmazásbiztonsági csoportot frissíti. |
> | Műveletek | Microsoft.Network/azurefirewalls/delete | Az Azure tűzfal törlése |
> | Műveletek | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/logDefinitions/read | Az Azure tűzfal események lekérése |
> | Műveletek | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/metricDefinitions/read | Az Azure tűzfal elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Network/azurefirewalls/read | Az Azure tűzfal beolvasása |
> | Műveletek | Microsoft.Network/azurefirewalls/write | Létrehoz vagy frissít egy Azure-tűzfal |
> | Műveletek | Microsoft.Network/bgpServiceCommunities/read | A Bgp Szolgáltatásközösségek beolvasása |
> | Műveletek | Microsoft.Network/checkTrafficManagerNameAvailability/action | A Traffic Manager relatív DNS-név rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/read | Kapcsolatok diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a kapcsolatok diagnosztikai beállításait |
> | Műveletek | Microsoft.Network/connections/providers/Microsoft.Insights/metricDefinitions/read | A kapcsolatok metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection SharedKey beolvasása |
> | Műveletek | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/write | Létrehoz vagy frissít egy meglévő VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Lekérdezi a VirtualNetworkGatewayConnection Vpn-eszköz konfigurálása |
> | Műveletek | Microsoft.Network/connections/write | Létrehoz vagy frissít egy meglévő VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | A DDoS elleni védelmi terv Proxy törlése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | A DDoS elleni védelmi csomag Proxyján definíció beolvasása |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Létrehoz egy DDoS elleni védelmi csomag Proxyján vagy a frissítések és a meglévő DDoS elleni védelmi csomag Proxyján |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/delete | A DDoS Protection-Díjcsomag törlése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/join/action | Csatlakozik a DDoS Protection-Díjcsomag |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/read | A DDoS Protection-Díjcsomag beolvasása |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/write | A DDoS elleni védelmi tervhez létrehozása vagy frissítése. a DDoS elleni védelmi tervhez  |
> | Műveletek | Microsoft.Network/dnsoperationresults/read | A DNS-művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Network/dnsoperationstatuses/read | DNS művelet állapotának beolvasása  |
> | Műveletek | Microsoft.Network/dnszones/A/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "A". |
> | Műveletek | Microsoft.Network/dnszones/A/read | Kérje le az "A" típusú rekordhalmaz JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/A/write | Létrehozni vagy frissíteni a DNS-zóna belül "A" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "AAAA". |
> | Műveletek | Microsoft.Network/dnszones/AAAA/read | Első típusú rekordhalmazok "AAAA", JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/write | Hozzon létre, vagy frissítse a DNS-zóna belül "AAAA" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/all/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Műveletek | Microsoft.Network/dnszones/CAA/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "CAA". |
> | Műveletek | Microsoft.Network/dnszones/CAA/read | Első típusú rekordhalmazok "CAA", JSON formátumban. A rekordhalmaz a TTL, címkék és etag tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/CAA/write | Létrehozni vagy frissíteni a DNS-zóna belül "CAA" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "CNAME". |
> | Műveletek | Microsoft.Network/dnszones/CNAME/read | Első típusú rekordhalmazok "CNAME", JSON formátumban. A rekordhalmaz a TTL, címkék és etag tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/write | Létrehozni vagy frissíteni a DNS-zóna belül "CNAME" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/delete | Törölje a DNS-zóna JSON formátumban. A zóna tulajdonságai közé tartozik a címkéket, az etag, numberOfRecordSets és maxNumberOfRecordSets. |
> | Műveletek | Microsoft.Network/dnszones/MX/delete | Távolítsa el a rekordhalmaz megadott név, és írja be az "MX" a DNS-zóna. |
> | Műveletek | Microsoft.Network/dnszones/MX/read | Első típusú rekordhalmazok "MX", JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/MX/write | Létrehozni vagy frissíteni a DNS-zóna belül az "MX" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/NS/delete | A DNS-rekordhalmaz NS típusú törlése |
> | Műveletek | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/NS/write | Létrehozza vagy frissíti a DNS NS típusú rekordhalmaz |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | A DNS-zóna diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a DNS-zóna diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | A DNS-zóna metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/dnszones/PTR/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "PTR". |
> | Műveletek | Microsoft.Network/dnszones/PTR/read | Első típusú rekordhalmazok "PTR", JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/PTR/write | Létrehozni vagy frissíteni a DNS-zóna belül "PTR" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/read | Első DNS-zónát, JSON formátumban. A zóna tulajdonságai közé tartozik a címkéket, az etag, numberOfRecordSets és maxNumberOfRecordSets. Vegye figyelembe, hogy ez a parancs nem kéri le a zónán belüli rekordhalmazok. |
> | Műveletek | Microsoft.Network/dnszones/recordsets/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Műveletek | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Műveletek | Microsoft.Network/dnszones/SOA/write | Létrehozza vagy frissíti a DNS-rekordhalmaz SOA típusú |
> | Műveletek | Microsoft.Network/dnszones/SRV/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a "SRV: a DNS-zóna. |
> | Műveletek | Microsoft.Network/dnszones/SRV/read | Első típusú rekordhalmazok "SRV", JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/SRV/write | Létrehozás vagy frissítés SRV típusú rekordhalmaz |
> | Műveletek | Microsoft.Network/dnszones/TXT/delete | Távolítsa el a rekordhalmaz megadott név, és írja be a DNS-zóna a "TXT". |
> | Műveletek | Microsoft.Network/dnszones/TXT/read | "TXT", az típusú rekordhalmazok első JSON formátumban. A rekordhalmaz rekordok, valamint az Élettartamot, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/TXT/write | Létrehozni vagy frissíteni a DNS-zóna belül "TXT" típusú rekordhalmazok. A megadott rekordok lecseréli a jelenlegi a rekordhalmaz-rekordokat. |
> | Műveletek | Microsoft.Network/dnszones/write | Hozzon létre, vagy frissítse a DNS-zóna egy erőforráscsoporton belül.  Frissítse a DNS-zóna erőforrásai a címkék segítségével. Vegye figyelembe, hogy ez a parancs nem használható létrehozni vagy frissíteni a zónán belüli rekordhalmazok. |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/delete | Az expressroute-kapcsolatcsoport sávszélességének engedély törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/read | Egy expressroute-kapcsolatcsoport sávszélességének engedélyezési beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/write | Létrehoz vagy frissít egy meglévő expressroute-kapcsolatcsoport sávszélességének engedélyezési |
> | Műveletek | Microsoft.Network/expressRouteCircuits/delete | Expressroute-kapcsolatcsoport sávszélességének törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/join/action | Egy Expressroute-Kapcsolatcsoporthoz csatlakozik |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés ArpTable beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Az expressroute-kapcsolatcsoport sávszélességének kapcsolat törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Az expressroute-kapcsolatcsoport sávszélességének kapcsolat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Létrehoz vagy frissít egy meglévő expressroute-kapcsolatcsoport sávszélességének kapcsolati erőforrás |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/delete | Egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/read | ExpressRoute-kapcsolatcsoport Társviszony diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az ExpressRoute-kapcsolatcsoport Társviszony diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/metricDefinitions/read | Az ExpressRoute-kapcsolatcsoport Társviszony metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/read | Lekérdezi egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés RouteTable beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Lekérdezi egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés útvonaltábla összegzése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Lekérdezi egy expressroute-kapcsolatcsoport sávszélességének társviszony-létesítés statisztika |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/write | Létrehoz vagy frissít egy meglévő expressroute-kapcsolatcsoport sávszélességének Társviszony |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Az ExpressRoute-Kapcsolatcsoportok diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az ExpressRoute-Kapcsolatcsoportok diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Az események beolvasása az ExpressRoute-Kapcsolatcsoportok számára |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Az ExpressRoute-Kapcsolatcsoportok metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/read | Expressroute-kapcsolatcsoport sávszélességének beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/stats/read | Egy expressroute-kapcsolatcsoport sávszélességének Stat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/write | Létrehoz vagy frissít egy meglévő expressroute-kapcsolatcsoport sávszélességének |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/delete | Törölje az Express Route közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/join/action | Az Express Route illesztések közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Az Express Route közötti kapcsolat társviszony-létesítés Arp-táblázat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Az Express Route közötti kapcsolat társviszony-létesítés törlése |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/read | Az Express Route közötti társviszony-létesítési kapcsolat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Az Express Route közötti társviszony-létesítés útvonaltábla kapcsolat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Lekérdezi az Express Route közötti kapcsolatok társviszony-létesítési útvonal tábla összegzése |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/write | Az Express Route közötti kapcsolat társviszony-létesítés létrehozása vagy frissítése. egy meglévő Express Route közötti kapcsolat társviszony-létesítés |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/read | Express-Route Get közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/write | Létrehozás vagy frissítés Express Route közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteGateways/delete | Expressroute-átjáró törlése |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Az Express Route-kapcsolat törlése |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Az Express Route-kapcsolat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Az Express Route-kapcsolat létrehozása vagy meglévő Express Route-kapcsolat frissítése |
> | Műveletek | Microsoft.Network/expressRouteGateways/join/action | Egy Expressroute-átjáró csatlakozik. |
> | Műveletek | Microsoft.Network/expressRouteGateways/read | Expressroute-átjáró |
> | Műveletek | Microsoft.Network/expressRouteGateways/write | Hozzon létre vagy Expressroute-átjáró frissítése |
> | Műveletek | Microsoft.Network/expressRoutePorts/delete | ExpressRoutePorts törlése |
> | Műveletek | Microsoft.Network/expressRoutePorts/join/action | ExpressRoutePorts csatlakozik |
> | Műveletek | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink beolvasása |
> | Műveletek | Microsoft.Network/expressRoutePorts/read | ExpressRoutePorts beolvasása |
> | Műveletek | Microsoft.Network/expressRoutePorts/write | Létrehozza vagy frissíti a ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePortsLocations/read | Get Express Route-portok helyek |
> | Műveletek | Microsoft.Network/expressRouteServiceProviders/read | Express Route-szolgáltatók beolvasása |
> | Műveletek | Microsoft.Network/frontdoors/read | Frontdoor beolvasása |
> | Műveletek | Microsoft.Network/getDnsResourceReference/action | A DNS alias erőforrás függőségi kérelem |
> | Műveletek | Microsoft.Network/internalNotify/action | A DNS alias az erőforrás-értesítés |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészlet csatlakozik |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/read | Lekérdezi egy load balancer háttérrendszer cím készlet definíciója |
> | Műveletek | Microsoft.Network/loadBalancers/delete | Terheléselosztó törlése |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Egy terhelés terheléselosztó előtérbeli IP-konfiguráció definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Csatlakozik a terheléselosztó bejövő nat-készlet |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/read | Lekéri a terheléselosztó bejövő nat-készlet definíciója |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/delete | Törli a terheléselosztó bejövő nat-szabály |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Csatlakozik a terheléselosztó bejövő nat-szabály |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/read | Lekéri a terheléselosztó bejövő nat-szabály meghatározása |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/write | Terheléselosztó bejövő nat-szabály létrehozása vagy frissítése egy meglévő terheléselosztó bejövő nat-szabálykészlet |
> | Műveletek | Microsoft.Network/loadBalancers/loadBalancingRules/read | Load balancer terhelés terheléselosztási szabálydefiníciót beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/networkInterfaces/read | Lekéri az összes hálózati adapter egy terheléselosztó alatt mutató hivatkozások |
> | Műveletek | Microsoft.Network/loadBalancers/outboundRules/read | Load balancer kimenő szabály definícióját beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó a mintavételezők használatával. Az ezen engedély healthProbe tulajdonságot a Virtuálisgép-méretezési csoport például set hivatkozhat a mintavétel. |
> | Műveletek | Microsoft.Network/loadBalancers/probes/read | Lekérdezi a terheléselosztói mintavételezők |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | A Load Balancer diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a Load Balancer diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | A terheléselosztó számára események lekérése |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | A terheléselosztó számára elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Network/loadBalancers/read | Load balancer definici beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/virtualMachines/read | Lekéri az összes virtuális gépet egy terheléselosztó alatt mutató hivatkozások |
> | Műveletek | Microsoft.Network/loadBalancers/write | Load balancer létrehozása vagy frissítése egy meglévő terheléselosztóhoz |
> | Műveletek | Microsoft.Network/localnetworkgateways/delete | Törli a LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/read | Lekérdezi a LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/write | Létrehoz vagy frissít egy meglévő LocalNetworkGateway |
> | Műveletek | Microsoft.Network/locations/availableDelegations/read | Rendelkezésre álló delegálásokat beolvasása |
> | Műveletek | Microsoft.Network/locations/bareMetalTenants/action | Foglalja le vagy ellenőrzi a bérlő egy operációs rendszer nélküli |
> | Műveletek | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Ellenőrzi a gyorsított hálózatkezelés támogatása |
> | Műveletek | Microsoft.Network/locations/checkDnsNameAvailability/read | Ellenőrzi, hogy dns-címke érhető el a megadott helyen |
> | Műveletek | Microsoft.Network/locations/effectiveResourceOwnership/action | Lekérdezi a hatékony erőforrás tulajdonosa |
> | Műveletek | Microsoft.Network/locations/operationResults/read | Egy aszinkron POST vagy a törlési művelet eredményének beolvasása |
> | Műveletek | Microsoft.Network/locations/operations/read | Lekérdezi a művelet erőforrás, amely egy aszinkron művelet állapotát jelöli. |
> | Műveletek | Microsoft.Network/locations/setResourceOwnership/action | Beállítja az erőforrás tulajdonosa |
> | Műveletek | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Lekérdezi a támogatott virtuális gépek méretei |
> | Műveletek | Microsoft.Network/locations/usages/read | Az erőforrásokat kapja meg a használati metrikák |
> | Műveletek | Microsoft.Network/locations/validateResourceOwnership/action | Érvényesíti az erőforrás tulajdonosa |
> | Műveletek | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Rendelkezésre álló virtuális hálózati Szolgáltatásvégpontok szolgáltatásainak listáját kéri le |
> | Műveletek | Microsoft.Network/networkIntentPolicies/delete | Egy hálózati szándék házirend törlése |
> | Műveletek | Microsoft.Network/networkIntentPolicies/read | Lekérdezi egy hálózati szándék szabályzat leírása |
> | Műveletek | Microsoft.Network/networkIntentPolicies/write | Egy hálózati szándékot házirend létrehoz vagy frissít egy meglévő hálózati szándékot házirend |
> | Műveletek | Microsoft.Network/networkInterfaces/delete | Hálózati adapter törlése |
> | Műveletek | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Az erőforrás diagnosztikai azonosító beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hálózati biztonsági csoportok konfigurált a hálózati adapter a virtuális gép beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | A virtuális gép hálózati illesztőjén konfigurált útválasztási táblázat beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/ipconfigurations/read | Lekérdezi egy hálózati adapter ip konfiguráció definíciója.  |
> | Műveletek | Microsoft.Network/networkInterfaces/join/action | Csatlakoztatja a virtuális gépek hálózati adapter |
> | Műveletek | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Egy erőforrás csatlakoztatja a hálózati illesztő keresztül a szolgáltatás-társítás |
> | Műveletek | Microsoft.Network/networkInterfaces/loadBalancers/read | A hálózati adapter részét képező összes terheléselosztók beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | A hálózati adapter elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/read | Lekérdezi egy hálózati illesztőjének definícióját.  |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/delete | A szolgáltatás-társítás törlése |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/read | Szolgáltatásdefiníció hozzárendelés beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Ellenőrzi a szolgáltatás-társítás |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/write | Egy új szolgáltatás társítást hoz létre vagy módosítja a meglévő Service társítás |
> | Műveletek | Microsoft.Network/networkInterfaces/write | Hálózati adapter létrehozása vagy frissítése egy meglévő hálózati adaptert.  |
> | Műveletek | Microsoft.Network/networkProfiles/delete | Hálózati profil törlése |
> | Műveletek | Microsoft.Network/networkProfiles/read | Hálózati profil beolvasása |
> | Műveletek | Microsoft.Network/networkProfiles/removeContainers/action | Eltávolítja a tárolók |
> | Műveletek | Microsoft.Network/networkProfiles/setContainers/action | Beállítja a tárolók |
> | Műveletek | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Tároló hálózati adapterek beállítása |
> | Műveletek | Microsoft.Network/networkProfiles/write | Létrehoz vagy frissít egy hálózati profilt |
> | Műveletek | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Egy alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/delete | Egy hálózati biztonsági csoport törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/join/action | Csatlakoztatja a hálózati biztonsági csoport |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése. Ezt a műveletet az adatelemzési erőforrás-szolgáltató egészíti ki adatokkal. |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | A hálózati biztonsági csoport lekérése eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/read | Egy hálózati biztonsági csoport definíció beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/delete | Biztonsági szabály törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/read | A biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/write | Biztonsági szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/write | A hálózati biztonsági csoport létrehozása vagy frissítése egy meglévő hálózati biztonsági csoport |
> | Műveletek | Microsoft.Network/networkWatchers/availableProvidersList/action | Az összes rendelkezésre álló internetes egy adott Azure-régióba tartozó szolgáltatók adja vissza. |
> | Műveletek | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Adja vissza a relatív késési pontszámának internetes szolgáltatók a megadott helyről az Azure-régióban. |
> | Műveletek | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfigurálja a célerőforrás csoportforgalom naplózása. |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/delete | A Kapcsolatfigyelő törlése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | A Kapcsolatfigyelő diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a kapcsolati figyelő diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | A Kapcsolatfigyelő elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/query/action | A lekérdezésben megadott végpontok közötti kapcsolat figyelése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/read | Első kapcsolat figyelése – részletek |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Indítsa el a megadott végpontok közötti kapcsolat figyelése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Állítsa le vagy szünetelteti a megadott végpontok közötti kapcsolat figyelése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/write | Hozza létre a Kapcsolatmonitorok |
> | Műveletek | Microsoft.Network/networkWatchers/connectivityCheck/action | Ellenőrzi a közvetlen TCP-kapcsolatot a virtuális gépről egy adott végpontot, beleértve egy másik virtuális Géphez vagy egy tetszőleges távoli kiszolgáló létrehozásának lehetőségét. |
> | Műveletek | Microsoft.Network/networkWatchers/delete | A hálózati figyelő törlése |
> | Műveletek | Microsoft.Network/networkWatchers/ipFlowVerify/action | Hogy a csomag engedélyezett vagy tiltott, vagy egy adott cél adja vissza. |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/delete | Törli az egy fókuszban |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/query/action | A lekérdezés egy megadott végpontot a hálózati forgalom figyelésére |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/read | Fókuszban részleteinek beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/start/action | A megadott végponton hálózati forgalom monitorozásának indítása |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/stop/action | Állítsa le vagy szünetelteti a hálózati forgalom a megadott végponton figyelése |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/write | Létrehoz egy fókuszban |
> | Műveletek | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | A diagnosztikai hálózati konfiguráció. |
> | Műveletek | Microsoft.Network/networkWatchers/nextHop/action | A célként megadott és a cél IP-cím lépjen vissza a következő ugrás típusa, és ezután Remélem, IP-cím. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/delete | Csomagrögzítés törlése |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Tulajdonságait és packet capture erőforrás adatainak beolvasása. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/read | A csomag rögzítési definíció beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/stop/action | A futó csomag-rögzítési munkamenet leállítása. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/write | Csomagrögzítés hoz létre |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/delete | Töröl egy PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh részleteinek beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/start/action | Indítsa el a megadott virtuális gépek közötti PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Állítsa le a megadott virtuális gépek közötti PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/write | Létrehoz egy PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Erőforrás-naplózás folyamat állapotának beolvasása. |
> | Műveletek | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Lekérdezi a hibaelhárítási eredmény a korábban futtatott vagy jelenleg fut a hibaelhárítási művelet. |
> | Műveletek | Microsoft.Network/networkWatchers/read | A hálózati figyelő-definíció beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/securityGroupView/action | Megtekintheti a konfigurált és érvényben lévő hálózati biztonsági csoport szabályai virtuális gépen alkalmazza. |
> | Műveletek | Microsoft.Network/networkWatchers/topology/action | A hálózati erőforrásokat és azok szintű nézet lekérdezi egy erőforráscsoportban. |
> | Műveletek | Microsoft.Network/networkWatchers/troubleshoot/action | Az Azure-beli hálózati erőforrás hibaelhárítás indítása. |
> | Műveletek | Microsoft.Network/networkWatchers/write | A network watcher létrehoz vagy frissít egy meglévő hálózati figyelőt |
> | Műveletek | Microsoft.Network/operations/read | Használható műveletek beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/delete | Nyilvános Ip-cím törlése. |
> | Műveletek | Microsoft.Network/publicIPAddresses/dnsAliases/delete | Törli az erőforrás nyilvános Ip-cím DNS-Alias |
> | Műveletek | Microsoft.Network/publicIPAddresses/dnsAliases/read | Egy nyilvános Ip-cím DNS-Alias erőforrás beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/dnsAliases/write | Létrehoz egy nyilvános Ip-cím DNS-Alias erőforrást |
> | Műveletek | Microsoft.Network/publicIPAddresses/join/action | Nyilvános ip-címmel csatlakozik |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Egy nyilvános IP-címet a terheléselosztó háttérkészletének törlése |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Egy nyilvános IP-címet a terheléselosztó háttérkészletének csatlakozik |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Lekérdezi egy nyilvános IP-cím load balancer háttérrendszer készlet definíciója |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Egy nyilvános IP-címet a terheléselosztó háttérkészletének létrehoz vagy frissít egy meglévő nyilvános IP-címet a terheléselosztó háttérkészletének |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | A nyilvános IP-cím diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre vagy nyilvános IP-cím a diagnosztikai beállítások frissítése |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | A nyilvános IP-cím naplódefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | A nyilvános IP-cím metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/read | Lekérdezi egy nyilvános IP-cím cím definíciója. |
> | Műveletek | Microsoft.Network/publicIPAddresses/write | Nyilvános Ip-cím létrehozása vagy frissítése egy meglévő nyilvános Ip-címet.  |
> | Műveletek | Microsoft.Network/publicIPPrefixes/delete | Töröl egy nyilvános Ip-előtag |
> | Műveletek | Microsoft.Network/publicIPPrefixes/join/action | Egy PublicIPPrefix csatlakozik |
> | Műveletek | Microsoft.Network/publicIPPrefixes/read | Egy nyilvános Ip-előtag-definíció beolvasása |
> | Műveletek | Microsoft.Network/publicIPPrefixes/write | Egy nyilvános Ip-előtag létrehoz vagy frissít egy meglévő nyilvános Ip-előtag |
> | Műveletek | Microsoft.Network/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.Network/routeFilters/delete | Töröl egy útvonal szűrődefiníciót |
> | Műveletek | Microsoft.Network/routeFilters/join/action | Egy útvonalszűrőhöz csatlakozik |
> | Műveletek | Microsoft.Network/routeFilters/read | Lekérdezi egy útvonal szűrődefiníciót |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/delete | Egy útvonal szűrődefiníciót szabály törlése |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/read | Lekérdezi egy útvonal szűrő szabály meghatározása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/write | Egy útvonalszűrő-szabály létrehozása vagy frissítése egy meglévő útvonalszűrő-szabály |
> | Műveletek | Microsoft.Network/routeFilters/write | Egy útvonalszűrőhöz létrehoz vagy frissít egy meglévő rotue szűrő |
> | Műveletek | Microsoft.Network/routeTables/delete | Töröl egy útválasztási tábla megadása |
> | Műveletek | Microsoft.Network/routeTables/join/action | Útválasztási táblázat csatlakozik |
> | Műveletek | Microsoft.Network/routeTables/read | Egy útválasztási tábla definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeTables/routes/delete | Egy útvonal-definíció törlése |
> | Műveletek | Microsoft.Network/routeTables/routes/read | Egy útvonal-definíció beolvasása |
> | Műveletek | Microsoft.Network/routeTables/routes/write | Útvonal létrehozása vagy frissítése egy meglévő útvonal |
> | Műveletek | Microsoft.Network/routeTables/write | Létrehoz egy útvonaltáblát, vagy egy meglévő rotue táblázat frissítése |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/delete | Törli az alkalmazás-szabálygyűjtemény egy biztonságos átjáróhoz |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/read | Az alkalmazás-szabálygyűjtemény lekérni az adott biztonságos átjáróhoz |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/write | Létrehozza vagy frissíti az alkalmazás-szabálygyűjtemény egy biztonságos átjáróhoz |
> | Műveletek | Microsoft.Network/securegateways/delete | Biztonságos átjáró törlése |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/delete | Biztonságos átjáró egy hálózati szabálygyűjtemény törlése |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/read | Biztonságos átjáró egy hálózati szabálygyűjtemény beolvasása |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/write | Létrehoz vagy frissít egy hálózati szabálygyűjtemény biztonságos átjáró |
> | Műveletek | Microsoft.Network/securegateways/providers/Microsoft.Insights/logDefinitions/read | Az Azure tűzfal események lekérése |
> | Műveletek | Microsoft.Network/securegateways/read | Biztonságos átjáró |
> | Műveletek | Microsoft.Network/securegateways/write | Létrehoz vagy frissít egy biztonságos átjáró |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/delete | Egy szolgáltatásvégpont-szabályzat törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/join/action | Csatlakozik egy szolgáltatásvégpont-szabályzat |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Egy alhálózathoz csatlakozik a szolgáltatásvégpont-szabályzatokra |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/read | Lekérdezi egy szolgáltatási végpont szabályzat leírása |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Szolgáltatási végpont Szabályzatdefiníció törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | A szolgáltatási végpont házirend definíció %{decription/ beolvasása |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Szolgáltatási végpont szabályzat-definíció létrehozása vagy frissítése egy meglévő végpontot Szabályzatdefiníció |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/write | Egy szolgáltatásvégpont-szabályzat létrehozása vagy frissítése egy meglévő szolgáltatásvégpont-szabályzatra |
> | Műveletek | Microsoft.Network/trafficManagerGeographicHierarchies/read | A Traffic Manager Geographic hierarchia tartalmazó régiók, amelyek a földrajzi forgalom-útválasztási módszer használható beolvasása |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Egy Azure-végpont törlése egy meglévő Traffic Manager-profilt. A TRAFFIC Manager útválasztási forgalom a törölt Azure-végpontra leáll. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Egy Azure-végpontot, amely a Traffic Manager-profil, többek között az, hogy az Azure a végpont tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Új Azure-végpont hozzáadása egy meglévő Traffic Manager-profilt, vagy egy meglévő Azure-végpontot, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/delete | A Traffic Manager-profil törlése. A Traffic Manager-profil társított összes beállítás el fog veszni, és a profil már nem használható irányíthatja a forgalmat. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont töröl egy meglévő Traffic Manager-profilt. A TRAFFIC Manager útválasztási forgalmat a törölt külső végpont leáll. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Külső végpont, amely a Traffic Manager-profil, többek között az, hogy a külső végpont tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Új külső végpont hozzáadása egy meglévő Traffic Manager-profilt, vagy egy meglévő külső végpontot az, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/heatMaps/read | A Traffic Manager Hőtérkép lekéri a megadott Traffic Manager-profilt, amely tartalmazza a hely és a forrás IP-cím alapján számát és a késési adatokat lekérdezni. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Beágyazott végpont töröl egy meglévő Traffic Manager-profilt. A TRAFFIC Manager útválasztási forgalom a törölt beágyazott végpontra leáll. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Beágyazott végpont, amely a Traffic Manager-profil, többek között az, hogy a beágyazott végpont tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Új beágyazott végpont hozzáadása egy meglévő Traffic Manager-profilt, vagy egy meglévő beágyazott végpontot a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | A Traffic Manager diagnosztikai beállításait |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a Traffic Manager-diagnosztikai beállítások tato operace je insights erőforrás-szolgáltató egészíti ki. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | A Traffic Managerhez események lekérése |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | A Traffic Manager esetében elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/read | A Traffic Manager-profil konfigurációjának beolvasása. Ez magában foglalja a DNS-beállítások, forgalom-útválasztási beállítások, végpont a figyelési beállításokat, és webproxykiszolgálókra irányítja a Traffic Manager-profil végpontjainak listájában. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager-profil létrehozása, vagy egy meglévő Traffic Manager-profil konfigurációjának módosításához.<br>Ez magában foglalja, engedélyezése vagy letiltása a profil, és módosítja a DNS-beállításait, a forgalom-útválasztási beállítások vagy a végpont a figyelési beállításokat.<br>A végpontokra irányítva a Traffic Manager-profil által hozzáadott, eltávolított, engedélyezhető vagy le van tiltva. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Törli a felhasználói metrikák valós idejű gyűjtemény használt előfizetés-szintű kulcs. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/read | Beolvassa a felhasználói metrikák valós idejű gyűjtemény használt előfizetés-szintű kulcs. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/write | Létrehoz egy új előfizetés-szintű kulcsot, valós idejű felhasználói mérőszámok gyűjtemény használható. |
> | Műveletek | Microsoft.Network/unregister/action | Az előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.Network/virtualHubs/delete | Töröl egy virtuális központtal |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Töröl egy HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Egy HubVirtualNetworkConnection beolvasása |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Létrehozás vagy frissítés egy HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/read | Egy virtuális központ beolvasása |
> | Műveletek | Microsoft.Network/virtualHubs/write | Létrehozás vagy frissítés egy virtuális központtal |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/delete | Töröl egy virtuális hálózati átjárónak |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | A virtualNetworkGateway VPN-ügyfele csomagjának létrehozása |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | A VirtualNetworkGateway VpnProfile csomagjának létrehozása |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Lekérdezi a virtualNetworkGateway hirdetett útvonalakat |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Lekérdezi a virtualNetworkGateway bgp-társak állapotát |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Lekérdezi a virtualnetworkgateway megtanult útvonalak |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | A VirtualNetworkGateway / Vpn-ügyfél kapcsolat állapotának lekérése |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | VPN-ügyfele Ipsec paraméterek lekérése VirtualNetworkGateway P2S-ügyfél. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Az URL-címét egy előre létrehozott vpn-profil ügyfélcsomag beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | A virtuális hálózati átjáró diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a virtuális hálózati átjáró diagnosztikai beállítások tato operace je insights erőforrás-szolgáltató egészíti ki. |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Virtuális hálózati átjáró események lekérése |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Virtuális hálózati átjáró elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/read | Lekérdezi egy virtuális hálózati átjárónak |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/reset/Action | Alaphelyzetbe állítja a virtualNetworkGateway |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Állítsa be a VPN-ügyfele Ipsec paraméterek VirtualNetworkGateway P2S-ügyfél. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Sorolja fel a támogatott Vpn-eszközök |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/write | Létrehozza vagy frissíti a VirtualNetworkGateway |
> | Műveletek | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ellenőrizze, hogy a megadott virtuális hálózat érhető el-e Ip-cím |
> | Műveletek | Microsoft.Network/virtualNetworks/customViews/get/action | Egy virtuális hálózat egyéni nézet tartalmának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/customViews/read | Virtuális hálózat egyéni nézet definíciójának lekérdezése |
> | Műveletek | Microsoft.Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/peer/action | Egy virtuális hálózatot és egy másik virtuális hálózat is társul |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Virtuális hálózati diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozni vagy frissíteni a diagnosztikai beállításokat a virtuális hálózat |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | A virtuális hálózat naplódefinícióinak beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read | Rendelkezésre álló metrikák beolvasása a PingMesh |
> | Műveletek | Microsoft.Network/virtualNetworks/read | A virtuális hálózati definíció beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Egy virtuális hálózati társviszony proxyjának törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Lekérdezi egy virtuális hálózati társviszony-létesítés proxydefiníció |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Egy virtuális hálózati társviszony proxyjának létrehoz vagy frissít egy meglévő virtuális hálózati társviszony proxyjának |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/delete | Törli a virtuális hálózat alhálózatának |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/join/action | Virtuális hálózat csatlakozik |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Például a storage-fiók vagy az SQL database erőforrás csatlakozik egy alhálózathoz. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/read | Lekérdezi egy virtuális hálózat alhálózati definíció |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Törli az erőforrás-navigációs hivatkozást |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Az erőforrás-navigációs hivatkozást-definíció beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Erőforrás-navigációs hivatkozást létrehoz vagy frissít egy meglévő erőforrás-navigációs hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | A szolgáltatás társítás hivatkozás törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Társítás hivatkozás részletei szolgáltatásdefiníció beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Lekérdezi egy szolgáltatás társítás hivatkozás definíciója |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Ellenőrzi a szolgáltatás társítás hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Szolgáltatás társítás hivatkozást hoz létre vagy frissíti a meglévő Service társítás hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | A virtuális gépek mutató hivatkozások lekérdezi a virtuális hálózat alhálózatán |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/write | Virtuális hálózat alhálózatának létrehozása vagy frissítése egy meglévő virtuális hálózat alhálózatához. |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | A címkézett fogyasztói törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | A forgalom fogyasztói címkézett-definíció beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | A címkézett fogyasztói ellenőrzi |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | A címkézett forgalom fogyasztói létrehoz vagy frissít egy meglévő címkézett forgalom fogyasztói |
> | Műveletek | Microsoft.Network/virtualNetworks/usages/read | Az egyes alhálózatokon, a virtuális hálózatnak az IP-usages beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualMachines/read | A virtuális gépek mutató hivatkozások lekérdezi egy virtuális hálózaton |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Egy virtuális hálózati társviszony törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Lekérdezi egy virtuális hálózati társviszony-létesítési definíciója |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | A virtuális hálózatok közötti társviszony létrehozása vagy frissítése. egy meglévő virtuális hálózatok közötti társviszony |
> | Műveletek | Microsoft.Network/virtualNetworks/write | Létrehoz egy virtuális hálózatot, vagy egy meglévő virtuális hálózat frissítése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/delete | Virtuális hálózati Tap törlése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/join/action | Csatlakoztatja a virtuális hálózati tap |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/read | Virtuális hálózati Tap beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/write | Létrehozni vagy frissíteni a virtuális hálózati Tap |
> | Műveletek | Microsoft.Network/virtualWans/delete | Törli a virtuális Wan |
> | Műveletek | Microsoft.Network/virtualWans/read | Get-virtuális Wan |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/delete | Töröl egy virtuális központ proxy |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/read | Lekérdezi egy virtuális központ proxydefiníció |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/write | Egy virtuális központ proxy létrehozása vagy frissítése egy virtuális központ proxy |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubs/read | Az összes virtuális Hubs hivatkozó virtuális Wan beolvasása. |
> | Műveletek | Microsoft.Network/virtualwans/vpnconfiguration/action | Vpn-konfiguráció beolvasása |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Törli a Vpn-hely proxy |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/read | Lekérdezi egy virtuális magánhálózati proxy helydefiníció |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/write | Egy Vpn-hely proxy létrehozása vagy frissítése egy Vpn-hely proxy |
> | Műveletek | Microsoft.Network/virtualWans/vpnSites/read | Az összes VPN-helyek hivatkozó virtuális Wan beolvasása. |
> | Műveletek | Microsoft.Network/virtualWans/write | Létre vagy nem frissíthető virtuális Wan |
> | Műveletek | Microsoft.Network/vpnGateways/delete | Törli a VPN-átjáróban. |
> | Műveletek | Microsoft.Network/vpnGateways/read | Lekérdezi egy VPN-átjáróban. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/read | Lekérdezi egy VPN-kapcsolat. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/write | Egy VPN-kapcsolat használatával. |
> | Műveletek | Microsoft.Network/vpnGateways/write | Egy VPN-átjáró használatával. |
> | Műveletek | Microsoft.Network/vpnsites/delete | Egy Vpn-hely erőforrás törlése. |
> | Műveletek | Microsoft.Network/vpnsites/read | Egy Vpn-hely erőforrás beolvasása. |
> | Műveletek | Microsoft.Network/vpnsites/write | Létrehozza vagy frissíti a Vpn-hely erőforrás. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Egy Namespace engedélyezési szabály létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai, valamint elsődleges és másodlagos kulcsai frissíthetők. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Értesítésiközpont-engedélyezési szabályok törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Az értesítési központhoz tartozó kapcsolati sztring beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Értesítésiközpont-engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai, valamint elsődleges és másodlagos kulcsai frissíthetők. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Leküldéses tesztértesítés küldése. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Értesítésiközpont-erőforrás törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Namespace metrikák erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Az összes értesítési központbeli PNS-hitelesítőadatok beolvasása. Ez magában foglalja a WNS, az MPNS, a APNS, a GCM és a Baidu hitelesítő adatok |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Az értesítésiközpont-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Egy értesítési központ létrehozása és tulajdonságainak frissítése. A tulajdonságok között főleg PNS hitelesítő adatainak. Az engedélyezési szabályok és a TTL |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/write | Namespace erőforrás létrehozása és tulajdonságainak frissítése. Címkék és a kapacitás, a Namespace olyan tulajdonságai frissíthetők. |
> | Műveletek | Microsoft.NotificationHubs/operationResults/read | A Notification Hubs-szolgáltató által végrehajtott művelet eredményeinek visszaadása |
> | Műveletek | Microsoft.NotificationHubs/operations/read | A Notification Hubs-szolgáltató által támogatott műveletek listájának visszaadása |
> | Műveletek | Microsoft.NotificationHubs/register/action | A Notification Hubs-erőforrásszolgáltatóra való előfizetés regisztrálása, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele |
> | Műveletek | Microsoft.NotificationHubs/unregister/action | A Notification Hubs-erőforrás-szolgáltató regisztrációjának megszüntetése, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/read | A Hyper-V fürt tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/read | A Hyper-V fürt tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/write | Létrehozza vagy frissíti a Hyper-V fürt |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/write | Létrehozza vagy frissíti a Hyper-V fürt |
> | Műveletek | Microsoft.OffAzure/HyperVSites/delete | A Hyper-V hely törlése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/delete | A Hyper-V hely törlése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/read | A Hyper-V gazdagép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/read | A Hyper-V gazdagép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/write | Létrehozza vagy frissíti a Hyper-V-gazdagép |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/write | Létrehozza vagy frissíti a Hyper-V-gazdagép |
> | Műveletek | Microsoft.OffAzure/HyperVSites/jobs/read | Egy Hyper-V feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/jobs/read | Egy Hyper-V feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/machines/read | A Hyper-V gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/machines/read | A Hyper-V gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/machines/stop/action | A Hyper-V gépek leállítása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/machines/stop/action | A Hyper-V gépek leállítása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/operationsstatus/read | A Hyper-V műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/operationsstatus/read | A Hyper-V műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/read | A Hyper-V hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/read | A Hyper-V hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/refresh/action | A Hyper-V a helyen belüli objektumok frissítése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/refresh/action | A Hyper-V a helyen belüli objektumok frissítése |
> | Műveletek | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Futtató fiókok Hyper-V tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Futtató fiókok Hyper-V tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/usage/read | A Hyper-V hely használatokban beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/usage/read | A Hyper-V hely használatokban beolvasása |
> | Műveletek | Microsoft.OffAzure/HyperVSites/write | Létrehozza vagy frissíti a Hyper-V-hely |
> | Műveletek | Microsoft.OffAzure/HyperVSites/write | Létrehozza vagy frissíti a Hyper-V-hely |
> | Műveletek | Microsoft.OffAzure/Operations/read | Az elérhető műveletek beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/delete | A VMware-hely törlése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/delete | A VMware-hely törlése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/jobs/read | A VMware-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/jobs/read | A VMware-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/read | A VMware-alapú gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/read | A VMware-alapú gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/stop/action | A VMware-alapú gépek leállítása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/stop/action | A VMware-alapú gépek leállítása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/operationsstatus/read | A VMware-műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/operationsstatus/read | A VMware-műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/read | A VMware-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/read | A VMware-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/refresh/action | Az objektumok egy VMware-hely frissítése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/refresh/action | Az objektumok egy VMware-hely frissítése |
> | Műveletek | Microsoft.OffAzure/VMwareSites/runasaccounts/read | A futtató fiókok VMware tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/runasaccounts/read | A futtató fiókok VMware tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/usage/read | A VMware-hely lebontott beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/usage/read | A VMware-hely lebontott beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter tulajdonságainak beolvasása |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/write | Létrehozza vagy frissíti a VMware vcenter-kiszolgáló |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/write | Létrehozza vagy frissíti a VMware vcenter-kiszolgáló |
> | Műveletek | Microsoft.OffAzure/VMwareSites/write | Létrehozza vagy frissíti a VMware-hely |
> | Műveletek | Microsoft.OffAzure/VMwareSites/write | Létrehozza vagy frissíti a VMware-hely |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationalInsights/linkTargets/read | Felsorolja a meglévő fiókokat, amelyek nem tartoznak az Azure-előfizetéssel. Az Azure-előfizetés összekapcsolása egy munkaterületet, ennek a műveletnek a munkaterület létrehozása művelet ügyfél id tulajdonsága által visszaadott ügyfél-azonosítót használja. |
> | Műveletek | Microsoft.OperationalInsights/register/action | Előfizetés regisztrálása az erőforrás-szolgáltató. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/action | Új motor használatával való keresés. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | V2 keresési séma lekérése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/action | Új motor használatával való keresés. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/schema/read | V2 keresési séma lekérése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Konfiguráció hatókörének törlése |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Konfiguráció hatókörének beolvasása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Konfiguráció hatókörének beállítása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/delete | Munkaterület adatforrásainak beolvasása törlése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/read | Munkaterület adatforrásainak beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/write | Munkaterület adatforrásainak létrehozása/frissítése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/delete | Munkaterület törlése. Ha a kapcsolódó munkaterület egy meglévő munkaterületet létrehozáskor a azt a kapcsolódó munkaterület nem törlődik. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Létrehoz egy regisztrációs tanúsítványt a munkaterülethez. Ezt a tanúsítványt a Microsoft System Center Operation Manager a munkaterülethez csatlakoztatandó szolgál. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Letiltja egy adott munkaterület intelligenciacsomagját. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Lehetővé teszi, hogy egy adott munkaterület intelligenciacsomagját. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Felsorolja az adott munkaterületben látható intelligenciacsomagot, és felsorolja, hogy a csomag engedélyezett vagy letiltott az adott munkaterületben. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Összekapcsolt szolgáltatások törlése az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/read | Összekapcsolt szolgáltatások beolvasása az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/write | Szolgáltatások létrehozása/frissítése társított az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/action | A munkaterület kulcslistájának beolvasása a munkaterület számára. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/read | A munkaterület kulcslistájának beolvasása a munkaterület számára. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/managementGroups/read | A System Center Operations Manager felügyeleti csoportok a munkaterülethez kapcsolódó lekéri a nevét és metaadatait. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Munkaterülethez tartozó Metrikadefiníciók beolvasása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | A munkaterületre vonatkozó értesítési beállításainak a felhasználó törlése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/read | A munkaterületre vonatkozó értesítési beállításainak a felhasználó beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Beállítani a munkaterületre vonatkozó értesítési beállításainak a felhasználó. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törlése a munkaterületről |
> | Műveletek | Microsoft.OperationalInsights/workspaces/read | Lekér egy meglévő munkaterületet |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Töröl egy mentett keresési lekérdezést |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/read | Lekérdezi egy mentett keresési lekérdezést |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/write | Létrehoz egy mentett keresési lekérdezést |
> | Műveletek | Microsoft.OperationalInsights/workspaces/schema/read | Lekéri a munkaterület keresési sémáját.  Keresési séma tartalmazza a közzétett mezőket és azok típusát. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/search/action | Végrehajt egy keresési lekérdezést |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület a megosztott kulcsok. Ezekkel a kulcsokkal a Microsoft Operational Insights-ügynökök kapcsolódni a munkaterülethez. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Törli a tárolókonfigurációt. Ezzel leállítja a Microsoft Operational Insights beolvassa az adatokat a tárfiókból. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Lekéri a tárolókonfigurációt. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Létrehoz egy új storage-konfigurációt. Ezek a beállítások segítségével adatok kérhetők le a hely egy meglévő tárfiókot. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/usages/read | Használati adatok beolvasása egy munkaterületet, beleértve a munkaterület által olvasott adatok mennyiségét. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/write | Létrehoz egy új munkaterületet, vagy egy meglévő munkaterületet mutató hivatkozások azáltal, hogy az ügyfél-azonosítót a meglévő munkaterületről. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/delete | Meglévő felügyeleti társítás törlése |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/read | Meglévő felügyeleti társítás beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/write | Új felügyeleti társítás létrehozása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/delete | Meglévő felügyeleti konfiguráció törlése |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/read | Meglévő felügyeleti konfiguráció beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/write | Hozzon létre egy új felügyeleti konfiguráció |
> | Műveletek | Microsoft.OperationsManagement/register/action | Előfizetés regisztrálása az erőforrás-szolgáltató. |
> | Műveletek | Microsoft.OperationsManagement/solutions/delete | Meglévő OMS-megoldás törlése |
> | Műveletek | Microsoft.OperationsManagement/solutions/read | Meglévő OMS-megoldás beolvasása |
> | Műveletek | Microsoft.OperationsManagement/solutions/write | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PolicyInsights/asyncOperationResults/read | Lekérdezi az aszinkron művelet eredményét. |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/action | A szabályzateseményekkel kapcsolatos adatok lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/queryResults/action | A szabályzat állapotaival kapcsolatos adatok lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/summarize/action | A szabályzat legutóbbi állapotait tartalmazó összegzés lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Elindít egy új megfelelőségi kiértékelést a kiválasztott hatókörre. |
> | Műveletek | Microsoft.PolicyInsights/register/action | Regisztrálja a Policy Insigths erőforrás-szolgáltatót, és lehetővé teszi az azzal végzett műveleteket. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Portal/dashboards/delete | Az irányítópult eltávolítása az előfizetésből. |
> | Műveletek | Microsoft.Portal/dashboards/read | Az előfizetéshez tartozó irányítópultok beolvasása. |
> | Műveletek | Microsoft.Portal/dashboards/write | Irányítópult felvétele vagy átalakítása előfizetéssé. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Ellenőrzi, hogy a megadott Power BI dedikált kapacitás neve érvényes, és nincs használatban. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/delete | Törli a Power BI dedikált kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | A Power BI dedikált kapacitás elérhető naplók beolvasása |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | A Power BI dedikált kapacitás elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/read | Lekéri a megadott Power BI dedikált kapacitás vonatkozó információkat. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/resume/action | Folytatja a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/skus/read | A kapacitás rendelkezésre álló SKU adatainak beolvasása |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/suspend/action | Felfüggeszti a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/write | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitás. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationresults/read | Lekéri a megadott műveleti eredményekre vonatkozó információkat. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationstatuses/read | A megadott művelet állapotára adatait kérdezi le. |
> | Műveletek | Microsoft.PowerBIDedicated/operations/read | Lekéri a műveletekre vonatkozó információkat |
> | Műveletek | Microsoft.PowerBIDedicated/register/action | A Power BI dedikált erőforrás-szolgáltató regisztrálása. |
> | Műveletek | Microsoft.PowerBIDedicated/skus/read | Lekéri a termékváltozatokra vonatkozó információkat |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Műveletek | Microsoft.RecoveryServices/locations/allocateStamp/action | Az allocatestamp a szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Műveletek | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási tárak biztonsági mentési állapotának ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/locations/operationStatus/read | Műveleti állapotának beolvasása egy adott művelethez |
> | Műveletek | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/register/action | Előfizetés regisztrálása egy adott erőforrás-szolgáltató |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/read | Konfigurációjának visszaadása Recovery Services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/write | Konfigurációjának frissítése Recovery Services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | A biztonsági másolat védelmi leképezésének törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | A biztonsági másolat védelmi leképezésének beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonsági másolat védelmi leképezésének létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Összes védhető tároló beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | A regisztrált tároló törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Tárolóban lévő számítási feladatok lekérdezése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | A tároló összes elemének lekérdezése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Védett elem törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatainak visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Kiépítés azonnali Elemhelyreállításának védett elem |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali visszavonása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | A védett elem biztonsági másolatának létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Egy regisztrált tároló létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | A tárolólista |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/read | Összes feladatobjektum visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Feladatexportálási művelet eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperations/read | Adja vissza a biztonsági mentési művelet állapotának helyreállítási tár. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Egy védelmi szabályzat törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirendművelet állapotának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Összes védelmi szabályzat visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Egy védelmi szabályzat létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tároló visszaadása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentés védelmi leképezések listázása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Biztonsági PIN-kódot ad vissza információkat helyreállítási tár. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Tárkonfigurációjának visszaadása a Recovery Services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Tároló konfigurációjának frissítése Recovery Services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Használati összegzéseket adja vissza a védett elemek és védett kiszolgálók egy Recovery Services. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A védett elem a művelet érvényesítése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tár hitelesítőadat-tanúsítványa. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/delete | A tár törlése művelettel törölhetők a megadott Azure-erőforrás "tároló" típusú |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a riasztások a Recovery services-tárolót. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | A helyreállítási tár értesítési konfigurációjának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | A helyreállítási tár e-mail-értesítéseinek konfigurálása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Az Azure Backup-diagnosztika |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Az Azure Backup-diagnosztika |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Azure Backup-naplók |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Az Azure Backup-metrikák |
> | Műveletek | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A szolgáltatástároló művelet használható egy tároló regisztrációja. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet eredményeinek beolvasása művelettel olvashatók a műveleti állapota és az aszinkron módon elküldött művelet eredményének beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel az erőforráshoz regisztrált tárolók beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatástároló regisztrálása művelettel egy tároló regisztrálni a helyreállítási szolgáltatás használható. |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Minden riasztási beállításainak olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Hozzon létre vagy bármely riasztás beállításainak frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationEvents/read | Minden olyan események olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Minden olyan hálók törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Folyamatkiszolgálói lemezkép üzembe helyezése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Fabric áttelepíteni az aad-be |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Minden olyan hálók olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újbóli társítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Háló eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Olvassa el a logikai hálózat |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatokban |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Minden olyan hálózati hozzárendelések törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Bármely Hálózatleképezések olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Létrehozás vagy frissítés bármely Hálózatleképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Védhető elem felderítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a alkalmazásvédelmi tárolókkal |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Védelmi tároló eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védhető elemek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Minden védett elemek törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Védett elem eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elem ismételt védelme |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Visszajelzés elküldése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Minden olyan cél számítási méretek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | A feladatátvételi teszt karbantartása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Minden védett cikkek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Bármely Védelmitároló-leképezések törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a bármely Védelmitároló-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Védelmitároló-leképezés eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Létrehozás vagy frissítés bármely Védelmitároló-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Védelmitároló-Váltás |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Létrehozás vagy frissítés alkalmazásvédelmi tárolókkal |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Bármely Recovery Services-szolgáltatók törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el a bármely Recovery Services-szolgáltatók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services-szolgáltató eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Létrehozás vagy frissítés bármely Recovery Services-szolgáltatók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Bármely Tárhelybesorolások olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Bármely Tárhelybesorolás-leképezések törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a bármely Tárhelybesorolás-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Létrehozás vagy frissítés bármely Tárhelybesorolás-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Bármely vCenters törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármely vCenters olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Létrehozás vagy frissítés bármely vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Létrehozás vagy frissítés bármely hálók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Feladat megszakítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a feladatok |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Feladat újraindítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Feladat folytatása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Bármely Hálózatleképezések olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Olvassa el a hálózatokban |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Bármely szabályzat törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Szabályzatok olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Bármely irányelveinek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Olvassa el a bármely Védelmitároló-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Olvassa el a alkalmazásvédelmi tárolókkal |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Bármelyik helyreállítási terv törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Bármelyik helyreállítási tervek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ismételt védelem helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi teszt helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Teszt feladatátvétel karbantartásának helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Bármilyen helyreállítási tervek létrehozása vagy módosítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Olvassa el a bármely Recovery Services-szolgáltatók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Olvassa el a bármely Tárhelybesorolás-leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Bármely Tárhelybesorolások olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationUsages/read | Minden replikációs tárhasználat olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Minden tároló replikálás állapotának olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Tár állapotának frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Bármely vCenters olvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/tokenInfo/read | A jogkivonat-Recovery Services-tároló adatainak visszaadása. |
> | Műveletek | Microsoft.RecoveryServices/vaults/usages/read | Bármely tárhasználat olvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Tártoken művelettel Társzintű Háttérműveletekhez beolvasni a tároló szint háttérbeli műveletek használható. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Relay/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Relay/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja inkább a checknameavailabiltiy API-t. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Az API-t a put műveletet. Put művelet meghívásával használja helyette a a Namespace-engedélyezési szabály frissítése... Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/write | Egy Namespace engedélyezési szabály létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai, valamint elsődleges és másodlagos kulcsai frissíthetők. |
> | Műveletek | Microsoft.Relay/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | A névtérhez hozzárendelt vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | A művelet HybridConnection frissíteni. Ez a művelet nem támogatott az API 2017-04-01-es verzióját. Az engedélyezési szabályok. Engedélyezési szabály frissítéséhez használja a Put művelet meghívásával. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | HybridConnection engedélyezési szabályok törlésére szolgáló művelet |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | A kapcsolati karakterlánc HybridConnection beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  HybridConnection engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai frissíthetők. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/write | Hozzon létre vagy frissítés HybridConnection tulajdonságai. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/read | Beolvassa az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/write | Frissíti az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.Relay/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | A művelet WcfRelay frissíteni. Ez a művelet nem támogatott az API 2017-04-01-es verzióját. Az engedélyezési szabályok. Engedélyezési szabály frissítéséhez használja a Put művelet meghívásával. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | WcfRelay engedélyezési szabályok törlésére szolgáló művelet |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | A kapcsolati karakterlánc WcfRelay beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  WcfRelay engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai frissíthetők. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/Delete | WcfRelay erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/read | WcfRelay erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/write | Hozzon létre vagy frissítés WcfRelay tulajdonságai. |
> | Műveletek | Microsoft.Relay/namespaces/write | Namespace erőforrás létrehozása és tulajdonságainak frissítése. Címkék és a kapacitás, a Namespace olyan tulajdonságai frissíthetők. |
> | Műveletek | Microsoft.Relay/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.Relay/register/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.Relay/unregister/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Beolvassa a megadott erőforrás rendelkezésre állási állapotát |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Műveletek | Microsoft.ResourceHealth/events/read | Az adott előfizetés Service Health-eseményeinek beolvasása |
> | Műveletek | Microsoft.Resourcehealth/healthevent/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Activated/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/InProgress/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Pending/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Resolved/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Updated/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.ResourceHealth/impactedResources/read | Az adott előfizetés érintett erőforrásainak beolvasása |
> | Műveletek | Microsoft.ResourceHealth/register/action | A Microsoft Resource Health-előfizetés regisztrálása |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Resources/checkPolicyCompliance/action | Annak ellenőrzése, hogy egy adott erőforrás megfelelőségi állapota megfelel-e az erőforrás-szabályzatoknak. |
> | Műveletek | Microsoft.Resources/checkResourceName/action | Erőforrásnév érvényességének ellenőrzése. |
> | Műveletek | Microsoft.Resources/deployments/cancel/action | Megszakítja az üzembe helyezést. |
> | Műveletek | Microsoft.Resources/deployments/delete | Törli az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/deployments/validate/action | Érvényesíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/links/delete | Törli az erőforrás-hivatkozást. |
> | Műveletek | Microsoft.Resources/links/read | Beolvassa vagy listázza az erőforrás-hivatkozásokat. |
> | Műveletek | Microsoft.Resources/links/write | Létrehozza vagy frissíti az erőforrás-hivatkozást. |
> | Műveletek | Microsoft.Resources/marketplace/purchase/action | Erőforrás vásárlása a Piactéren. |
> | Műveletek | Microsoft.Resources/providers/read | A szolgáltatók listájának lekérése. |
> | Műveletek | Microsoft.Resources/resources/read | Az erőforrások listájának beolvasása szűrők alapján. |
> | Műveletek | Microsoft.Resources/subscriptions/locations/read | Beolvassa a támogatott helyek listáját. |
> | Műveletek | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Műveletek | Microsoft.Resources/subscriptions/providers/read | Beolvassa vagy listázza az erőforrás-szolgáltatókat. |
> | Műveletek | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/delete | Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Az üzembe helyezési műveletek állapotának beolvasása vagy listázása. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Erőforrásokat helyez át az egyik erőforráscsoportból egy másikba. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Az erőforráscsoporthoz tartozó erőforrások lekérése. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Erőforrások erőforráscsoportok közötti áthelyezésének ellenőrzése. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/write | Létrehozza vagy frissíti az erőforráscsoportot. |
> | Műveletek | Microsoft.Resources/subscriptions/resources/read | Beolvassa az előfizetéshez tartozó erőforrásokat. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/delete | Törli az előfizetéscímkét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/read | Beolvassa vagy listázza az előfizetéscímkéket. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Törli az előfizetéscímke értékét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Beolvassa vagy listázza az előfizetéscímkék értékeit. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Hozzáadja az előfizetéscímke értékét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/write | Előfizetéscímke hozzáadása. |
> | Műveletek | Microsoft.Resources/tenants/read | Beolvassa a bérlők listáját. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Scheduler/jobcollections/delete | A feladatgyűjtemény törlése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/disable/action | A feladatgyűjtemény letiltása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/enable/action | A feladatgyűjtemény engedélyezése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/delete | A feladat törlése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Logic App-definíció előállítása egy Scheduler-feladat alapján. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | A feladatelőzmények beolvasása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/read | A feladat beolvasása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/run/action | A feladat futtatása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/write | Feladatok létrehozása és frissítése |
> | Műveletek | Microsoft.Scheduler/jobcollections/read | Feladatgyűjtemény beolvasása |
> | Műveletek | Microsoft.Scheduler/jobcollections/write | Feladatgyűjtemények létrehozása és frissítése |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Search/checkNameAvailability/action | A szolgáltatásnév elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Search/operations/read | Felsorolja az összes, a Microsoft.Search szolgáltató elérhető műveleteinek. |
> | Műveletek | Microsoft.Search/register/action | A keresési erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Műveletek | Microsoft.Search/searchServices/createQueryKey/action | A lekérdezési kulcsot hoz létre. |
> | Műveletek | Microsoft.Search/searchServices/delete | A keresési szolgáltatás törlése. |
> | Műveletek | Microsoft.Search/searchServices/deleteQueryKey/delete | A lekérdezési kulcs törlése. |
> | Műveletek | Microsoft.Search/searchServices/diagnosticSettings/read | Lekérdezi a diganostic beállítása az erőforráshoz olvasási |
> | Műveletek | Microsoft.Search/searchServices/diagnosticSettings/write | Az erőforrás a diganostic beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Search/searchServices/listAdminKeys/action | Az adminisztrációs kulcsok beolvasása. |
> | Műveletek | Microsoft.Search/searchServices/listQueryKeys/read | Az adott Azure Search szolgáltatás lekérdezési API-kulcsok listáját adja vissza. |
> | Műveletek | Microsoft.Search/searchServices/logDefinitions/read | Naplók beolvasása. a keresési szolgáltatás |
> | Műveletek | Microsoft.Search/searchServices/metricDefinitions/read | A keresési szolgáltatás elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Search/searchServices/read | A keresési szolgáltatás beolvassa. |
> | Műveletek | Microsoft.Search/searchServices/regenerateAdminKey/action | Az adminisztrátori kulcs újragenerálása. |
> | Műveletek | Microsoft.Search/searchServices/start/action | A keresési szolgáltatás elindul. |
> | Műveletek | Microsoft.Search/searchServices/stop/action | A keresési szolgáltatás leállítása. |
> | Műveletek | Microsoft.Search/searchServices/write | Létrehozza vagy frissíti a keresési szolgáltatás. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/read | A komplex veszélyforrások elleni védelmi beállításait az erőforrás beolvasása |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/write | A komplex veszélyforrások elleni védelmi beállításait az erőforrás frissítése |
> | Műveletek | Microsoft.Security/alerts/read | Az összes elérhető biztonsági riasztások beolvasása |
> | Műveletek | Microsoft.Security/applicationWhitelistings/read | Az alkalmazás whitelistings beolvasása |
> | Műveletek | Microsoft.Security/applicationWhitelistings/write | Létrehoz egy új alkalmazásengedélyezés bevezetését, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/complianceResults/read | Az erőforrás a megfelelőségi eredmények beolvasása |
> | Műveletek | Microsoft.Security/locations/alerts/activate/action | Egy biztonsági riasztás aktiválása |
> | Műveletek | Microsoft.Security/locations/alerts/dismiss/action | Egy biztonsági riasztás bezárása |
> | Műveletek | Microsoft.Security/locations/alerts/read | Az összes elérhető biztonsági riasztások beolvasása |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | A just-in-time hálózati hozzáférési házirend törlése |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Just-in-time hálózati hozzáférési házirend irányuló kérelmet kezdeményez |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/read | A just-in-time hálózati hozzáférési szabályzatok beolvasása |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Létrehoz egy új just-in-time hálózati hozzáférési házirendet, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/locations/read | Lekérdezi a biztonsági adatok helye |
> | Műveletek | Microsoft.Security/locations/tasks/activate/action | Biztonsági ajánlás aktiválása |
> | Műveletek | Microsoft.Security/locations/tasks/dismiss/action | A biztonsági javaslatok elvetése |
> | Műveletek | Microsoft.Security/locations/tasks/read | Lekéri az összes rendelkezésre álló biztonsági ajánlás |
> | Műveletek | Microsoft.Security/locations/tasks/resolve/action | Biztonsági ajánlás feloldása |
> | Műveletek | Microsoft.Security/locations/tasks/start/action | Indítsa el a biztonsági ajánlás |
> | Műveletek | Microsoft.Security/policies/read | A biztonsági házirend beolvasása |
> | Műveletek | Microsoft.Security/policies/write | A biztonsági házirend frissítése |
> | Műveletek | Microsoft.Security/pricings/delete | A hatókör az árképzési beállítások törlése |
> | Műveletek | Microsoft.Security/pricings/read | A hatókör díjszabási beállításait |
> | Műveletek | Microsoft.Security/pricings/write | A hatókör az árképzési beállítások frissítése |
> | Műveletek | Microsoft.Security/register/action | Az Azure Security Center az előfizetés regisztrálása |
> | Műveletek | Microsoft.Security/securityContacts/delete | Törli a biztonsági kapcsolattartó |
> | Műveletek | Microsoft.Security/securityContacts/read | A biztonsági kapcsolattartó beolvasása |
> | Műveletek | Microsoft.Security/securityContacts/write | Frissíti a biztonsági kapcsolattartó |
> | Műveletek | Microsoft.Security/securitySolutions/delete | Biztonsági megoldás törlése |
> | Műveletek | Microsoft.Security/securitySolutions/read | Lekérdezi a biztonsági megoldások |
> | Műveletek | Microsoft.Security/securitySolutions/write | Létrehoz egy új biztonsági megoldást, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/securitySolutionsReferenceData/read | Lekérdezi a biztonsági megoldások referenciaadatok |
> | Műveletek | Microsoft.Security/securityStatuses/read | Lekérdezi a biztonsági állapot állapotok az Azure-erőforrásokhoz |
> | Műveletek | Microsoft.Security/securityStatusesSummaries/read | A biztonsági állapot-összefoglalók lekérdezi a hatókör |
> | Műveletek | Microsoft.Security/tasks/read | Lekéri az összes rendelkezésre álló biztonsági ajánlás |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/read | Lekérdezi a webes alkalmazás tűzfalak |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/write | Létrehoz egy új webalkalmazási tűzfal vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Security/workspaceSettings/connect/action | Munkaterület beállításainak újracsatlakozás beállításainak módosítása |
> | Műveletek | Microsoft.Security/workspaceSettings/delete | Törli a munkaterület beállításai |
> | Műveletek | Microsoft.Security/workspaceSettings/read | A munkaterület beállításainak lekérése |
> | Műveletek | Microsoft.Security/workspaceSettings/write | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/delete | Diagnosztikai beállítás törlése |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/read | Diagnosztikai beállítás beolvasása |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/write | Diagnosztikai beállítás írása |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettingscategories/read | A diagnosztikai beállítás kategóriák olvasása |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceBus/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ServiceBus/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja inkább a checknameavailabiltiy API-t. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Az API-t a put műveletet. Put művelet meghívásával használja helyette a a Namespace-engedélyezési szabály frissítése... Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/write | Egy Namespace engedélyezési szabály létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai, valamint elsődleges és másodlagos kulcsai frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | A névtérhez hozzárendelt vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | A névtérhez társított Event Grid-szűrő törlése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/read | A névtérhez társított Event Grid-szűrő beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/write | A névtérhez társított Event Grid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventhubs/read | Az EventHub-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/read | Beolvassa az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/write | Frissíti az egy névtérhez tartozó üzenetküldési Díjcsomag.<br>Ez az API elavult.<br>A MessagingPlan erőforráson keresztül elérhető tulajdonságok lettek helyezve (szülő) Namespace erőforrás API újabb verzióiban...<br>Ez a művelet nem támogatott az API 2017-04-01-es verzióját. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrate/action | Névtérmigrálási művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | A Migrálási konfiguráció törlése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Az áttelepítési konfiguráció beolvasása, amely megadja az áttelepítés és a függőben lévő replikálási műveletek állapotát |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | A standard szintű névtérből prémium szintűbe való áttelepítés visszaállítása |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | A standard szintű névtérhez hozzárendelt DNS társítása a prémium szintű névtérhez, mely művelet elvégzi az áttelepítést, és leállítja az erőforrások a standard szintű névtérből a prémium szintűbe való szinkronizálását |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Létrehoz vagy frissítések-Migrálás konfigurációjának. Ekkor elindul a prémium szintű névtérre váltani a szabványostól erőforrások szinkronizálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Namespace naplók erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | A művelet üzenetsor frissítésekor. Ez a művelet nem támogatott az API 2017-04-01-es verzióját. Az engedélyezési szabályok. Engedélyezési szabály frissítéséhez használja a Put művelet meghívásával. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Üzenetsor-engedélyezési szabályok törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Az üzenetsor kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Üzenetsor-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Várólista-engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/Delete | Üzenetsor-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/read | Üzenetsor-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/write | Hozzon létre vagy frissítés várólista-tulajdonságok. |
> | Műveletek | Microsoft.ServiceBus/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | A témakör frissítése művelet. Ez a művelet nem támogatott az API 2017-04-01-es verzióját. Az engedélyezési szabályok. Engedélyezési szabály frissítéséhez használja a Put művelet meghívásával. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | A témakör az engedélyezési szabályok törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | A témakör a kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  A témakör az engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | A témakör az engedélyezési szabályok létrehozása és tulajdonságainak frissítése. Az engedélyezési szabályok hozzáférési jogosultságai frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/Delete | A témakör erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/read | A témakör erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | A szabály erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | A szabály erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Hozzon létre vagy frissítési szabály tulajdonságai. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Hozzon létre vagy frissítés TopicSubscription tulajdonságai. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/write | Hozzon létre vagy az Update-témakör tulajdonságait. |
> | Műveletek | Microsoft.ServiceBus/namespaces/write | Namespace erőforrás létrehozása és tulajdonságainak frissítése. Címkék és a kapacitás, a Namespace olyan tulajdonságai frissíthetők. |
> | Műveletek | Microsoft.ServiceBus/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.ServiceBus/register/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Műveletek | Microsoft.ServiceBus/sku/read | Termékváltozat-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/sku/regions/read | SkuRegions erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/unregister/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/delete | Tetszőleges alkalmazás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/read | Tetszőleges alkalmazás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/delete | Tetszőleges szolgáltatás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Tetszőleges partíció beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Tetszőleges replika beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/read | Tetszőleges szolgáltatás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Bármely szolgáltatásállapot beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/write | Tetszőleges szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/write | Tetszőleges alkalmazás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Tetszőleges alkalmazástípus törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/read | Tetszőleges alkalmazástípus beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Tetszőleges alkalmazástípus-verzió törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Tetszőleges alkalmazástípus-verzió beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Tetszőleges alkalmazástípus-verzió létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/write | Tetszőleges alkalmazástípus létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/delete | Tetszőleges fürt törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/nodes/read | Tetszőleges csomópont beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/read | Tetszőleges fürt beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/statuses/read | Bármely fürtállapot beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/write | Tetszőleges fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/locations/clusterVersions/read | Minden fürtverzió beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Tetszőleges fürtverzió beolvasása egy adott környezetben |
> | Műveletek | Microsoft.ServiceFabric/locations/operationresults/read | Tetszőleges műveleti eredmények beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/operations/read | Tetszőleges műveletek beolvasása hely szerint |
> | Műveletek | Microsoft.ServiceFabric/operations/read | Tetszőleges használható műveletek beolvasása |
> | Műveletek | Microsoft.ServiceFabric/register/action | Tetszőleges művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SignalRService/checknameavailability/action | Ellenőrzi, hogy a név elérhető egy új szolgáltatással a SignalR |
> | Műveletek | Microsoft.SignalRService/register/action | A "Microsoft.SignalRService" erőforrás-szolgáltató regisztrálása az előfizetéshez |
> | Műveletek | Microsoft.SignalRService/SignalR/delete | A teljes SignalR törlése |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | A SignalR elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.SignalRService/SignalR/read | A SignalR beállítások és konfigurációk megtekintése a kezelési portál vagy API-n keresztül |
> | Műveletek | Microsoft.SignalRService/SignalR/write | Módosítsa a SignalR beállítások és konfigurációk a kezelési portál vagy API-n keresztül |
> | Műveletek | Microsoft.SignalRService/unregister/action | Az előfizetés "Microsoft.SignalRService" erőforrás-szolgáltató regisztrációjának törlése |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/delete | Alkalmazásdefiníció eltávolítása. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/read | Az alkalmazásdefiníciók listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/write | Alkalmazásdefiníció hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.Solutions/applications/delete | Alkalmazás eltávolítása. |
> | Műveletek | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applications/write | Létrehoz egy alkalmazást. |
> | Műveletek | Microsoft.Solutions/locations/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.Solutions/register/action | Regisztrálás megoldásokra. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Sql/checkNameAvailability/action | Ellenőrizze, hogy a megadott kiszolgáló neve az adott előfizetés világszerte kiosztásához rendelkezésre álló. |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend Set művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgáló blob naplózási házirend Set művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/capabilities/read | A funkciók beolvasása ehhez az előfizetéshez egy adott helyen |
> | Műveletek | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/locations/databaseOperationResults/read | Adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Lekérdezi a folyamatban lévő műveleteket a kiszolgáló törlése |
> | Műveletek | Microsoft.Sql/locations/deletedServerOperationResults/read | Lekérdezi a folyamatban lévő műveleteket a kiszolgáló törlése |
> | Műveletek | Microsoft.Sql/locations/deletedServers/read | A törölt kiszolgálók vagy lekérdezi a megadott kiszolgáló törlése tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/locations/deletedServers/recover/action | Egy törölt server helyreállítása |
> | Műveletek | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Virtuális hálózati szabályok társított virtuális hálózat vagy alhálózat törlése |
> | Műveletek | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | A rugalmas készlet aszinkron művelet azure aszinkron művelet beolvasása |
> | Műveletek | Microsoft.Sql/locations/elasticPoolOperationResults/read | Rugalmas készlet művelet eredményének beolvasása. |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend Set művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | A kiterjesztett kiszolgáló blob naplózási házirend Set művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/delete | Egy meglévő példányt feladatátvételi csoport törlése. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Tervezett feladatátvétel végrehajtása létező példány feladatátvételi csoportba. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvétel futtatják a meglévő példány feladatátvételi csoporthoz. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/read | A példány feladatátvételi csoportokat vagy a megadott példány feladatátvételi csoport tulajdonságainak lekérése adja vissza. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/write | Egy példány feladatátvételi csoportot hoz létre a megadott paramétereket, vagy a Tulajdonságok vagy címkék a megadott példány feladatátvételi csoport frissítése. |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionBackups/read | Megjeleníti a hosszú távú megőrzési időtartamú biztonsági mentések minden adatbázis minden kiszolgálón egy helyen |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Megjeleníti a hosszú távú megőrzési időtartamú biztonsági mentések minden adatbázis-kiszolgálón |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | A hosszú távú adatmegőrzés céljából készített biztonsági törlése |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Megjeleníti a hosszú távú megőrzési időtartamú biztonsági mentések adatbázis |
> | Műveletek | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Felügyelt adatbázis-visszaállítási művelet befejeződik |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Lekérdezi a folyamatban lévő műveleteket a felügyelt adatbázis transzparens adattitkosítás |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Lekérdezi a folyamatban lévő műveleteket a felügyelt adatbázis transzparens adattitkosítás |
> | Műveletek | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Egy adott hálózati adapter Azure aszinkron művelet adatai |
> | Műveletek | Microsoft.Sql/locations/networkInterfaceOperationResults/read | A megadott hálózati adapter művelet adatai |
> | Műveletek | Microsoft.Sql/locations/read | Lekérdezi az elérhető helyek egy adott előfizetéshez |
> | Műveletek | Microsoft.Sql/locations/syncAgentOperationResults/read | A szinkronizálási ügynök erőforrás-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncDatabaseIds/read | A szinkronizálási adatbázishoz azonosítóinak egy adott régió és az előfizetés |
> | Műveletek | Microsoft.Sql/locations/syncGroupOperationResults/read | A szinkronizálási csoport erőforrás-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncMemberOperationResults/read | A szinkronizálási tag erőforrás-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/usages/read | Használati metrikák gyűjteménye lekérdezi az előfizetés egy helyen |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Visszaadja a megadott virtuális hálózati szabályok részleteinek azure aszinkron művelet  |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | A megadott virtuális hálózati szabályok művelet adatai  |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/delete | Törli a meglévő felügyelt példány rendszergazdája. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/read | Felügyelt példány a rendszergazdák listáját kéri le. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/write | Létrehozza vagy frissíti a felügyelt példány rendszergazdai a megadott paraméterekkel. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/delete | Törli a meglévő felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/read | Lekérdezi a meglévő felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Az adatbázis fenyegetésészlelési szabályzatát a következőn: egy adott felügyelt adatbázis részleteinek lekérése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Módosítsa az adatbázis fenyegetésészlelési szabályzatát egy adott felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityEvents/read | A felügyelt adatbázis-biztonsági események beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Az adatbázis transzparens adattitkosítás, adott felügyelt adatbázis részleteinek lekérése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Az adatbázis transzparens adattitkosítás egy adott felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | A biztonságirés-értékelési egy adott adatbázis eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Egy adott adatbázisban konfigurált biztonságirés-értékelési részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | A sebezhetőségi felmérés szabály alapkonfigurációja egy adott adatbázis eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | A sebezhetőségi felmérés szabály alapkonfigurációja beolvasása egy adott adatbázishoz |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | A sebezhetőségi felmérés szabály alapkonfigurációja egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Egy meglévő vizsgálati eredménynek emberi olvasható formátumba konvertálja. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Biztonságirés-értékelési adatbázis vizsgálat végrehajtása. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Adatbázis biztonsági rések listáját értékelési vizsgálat rekordot ad vissza, vagy a vizsgálat rekord lekérése a megadott vizsgálat azonosítóját. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | A biztonságirés-értékelési egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/write | Új adatbázis létrehozása vagy frissítése egy meglévő adatbázist. |
> | Műveletek | Microsoft.Sql/managedInstances/delete | Törli a meglévő felügyelt példányt. |
> | Műveletek | Microsoft.Sql/managedInstances/encryptionProtector/read | Kiszolgáló titkosítási protectors listáját adja vissza, vagy a Tulajdonságok lekérdezi a megadott kiszolgáló titkosítási védelme. |
> | Műveletek | Microsoft.Sql/managedInstances/encryptionProtector/write | A tulajdonságainak frissítése az a megadott kiszolgáló titkosítási védelme. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/delete | Törli a meglévő Azure SQL felügyelt példánya kulcsot. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/read | A felügyelt példány listáját a kulcsok vagy a felügyelt példány megadott kulcs tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Sql/managedInstances/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott felügyelt példány kulcs frissítése. |
> | Műveletek | Microsoft.Sql/managedInstances/metricDefinitions/read | Felügyelt példány metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/metrics/read | Felügyelt példány metrikáinak beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/read | A felügyelt példányok vagy lekérdezi a megadott felügyelt példány tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/read | A felügyelt kiszolgáló fenyegetésészlelési szabályzatát egy adott felügyelt kiszolgálón konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/write | A felügyelt kiszolgáló adott felügyelt kiszolgálóra vonatkozó fenyegetésészlelési szabályzatát módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/tdeCertificates/action | Tanúsítvány létrehozása vagy frissítése TDE |
> | Műveletek | Microsoft.Sql/managedInstances/write | Felügyelt példány a megadott paraméterekkel vagy a Tulajdonságok vagy címkék a megadott felügyelt példány frissítése. |
> | Műveletek | Microsoft.Sql/operations/read | Lekérdezi az elérhető REST-műveletek |
> | Műveletek | Microsoft.Sql/register/action | A Microsoft SQL Database erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft.Sql/servers/administratorOperationResults/read | Lekérdezi a folyamatban lévő műveleteket a kiszolgáló-rendszergazdák |
> | Műveletek | Microsoft.Sql/servers/administrators/delete | Kiszolgálói rendszergazda törlése |
> | Műveletek | Microsoft.Sql/servers/administrators/read | Kiszolgálói rendszergazda részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/administrators/write | Létrehozni vagy frissíteni a kiszolgáló-rendszergazda |
> | Műveletek | Microsoft.Sql/servers/advisors/read | A kiszolgáló elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/read | Az a kiszolgáló megadott advisor ajánlott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/write | Alkalmazza a javasolt műveletet a kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/advisors/write | Frissítések automatikus – hajtsa végre a kiszolgáló szintjén tanácsadó állapotát. |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/read | A naplózás a megadott kiszolgálón konfigurált szabályzatot alapértelmezett kiszolgáló-táblázat részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/write | Módosítsa az alapértelmezett kiszolgáló táblanaplózás egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/operationResults/read | A kiszolgáló blob naplózási házirend Set művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/read | A kiszolgáló blob naplózási házirend a megadott kiszolgálón konfigurált részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/write | Módosítsa a kiszolgálószintű blobnaplózás egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/read | Adja vissza a kiszolgáló automatikus hangolási beállításai |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/write | A kiszolgáló automatikus hangolási beállításai frissül, és adja vissza a beállítások frissítve |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Törli a meglévő backup-archív tároló. |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | A művelettel egy biztonsági másolat hosszú távú adatmegőrzési-tár tartalmának beolvasása. Ezen a kiszolgálón regisztrált a tárolóval kapcsolatos információkat ad vissza |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ezzel a művelettel egy biztonsági másolat hosszú távú megőrzési regisztrálni egy kiszolgáló-tároló |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/delete | Töröl egy meglévő kiszolgáló kommunikációs kapcsolat. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/read | A megadott kiszolgáló kommunikációs kapcsolatok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/write | Létrehozása vagy frissítése egy kiszolgálón kommunikációs kapcsolat. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/read | Kiszolgáló kapcsolati házirendek a megadott kiszolgáló listájának visszaadása. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/write | Hozzon létre, vagy egy kiszolgáló kapcsolat-házirend frissítése. |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/read | Az adatbázis elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Megadott advisort az adatbázishoz az ajánlott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | A javasolt műveletet alkalmazza az adatbázisban |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/write | Frissítés automatikus – hajtsa végre az adatbázisszintű tanácsadó állapotát. |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/read | A tábla egy adott adatbázisban a következőn: naplózási házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/write | A tábla egy adott adatbázis naplózási szabályzatát módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/read | A blob egy adott adatbázisban a következőn: naplózási házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/write | A blob egy adott adatbázis naplózási szabályzatát módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/read | Adja vissza egy adatbázis automatikus hangolási beállításai |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/write | Frissíti az adatbázis automatikus hangolási beállításai, és adja vissza a beállítások frissítve |
> | Műveletek | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Vissza a megadott adatbázis biztonsági mentési, archiválási szabályzatok listájában. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Hozzon létre vagy nem frissíthető az adatbázis biztonsági mentési, archiválási házirend. |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/read | A kapcsolódási szabályzat egy adott adatbázisban konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/write | Egy adott adatbázishoz tartozó kapcsolódási szabályzat módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Adatbázis adatmaszkolási házirendek listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Egy adott adatbázishoz tartozó házirendszabály adatmaszkolási törlése |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Az adatok maszkolása a szabályzatbeli szabály egy adott adatbázisban konfigurált részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Egy adott adatbázishoz tartozó házirendszabály adatmaszkolási módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Adatmaszkolás egy adott adatbázishoz tartozó házirend módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Az elosztott lekérdezési lépés tárolt információt adhatja vissza data warehouse-lekérdezés a kiválasztott lépést azonosító |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Az adatraktár terjesztési lekérdezés adatait a kijelölt lekérdezés Azonosítóját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | A felhasználói tevékenység fut, és a felfüggesztett lekérdezéseket tartalmaz, amely az SQL Data Warehouse-példány beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/delete | Töröl egy meglévő adatbázist. |
> | Műveletek | Microsoft.Sql/servers/databases/export/action | Az Azure SQL-adatbázis exportálása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | A bővített blob egy adott adatbázisban a következőn: naplózási házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Egy adott adatbázishoz tartozó bővített blob naplózási szabályzatának módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/read | Az adatbázis-bővítmények gyűjtemény beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/write | A bővítmény egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Georedundáns biztonsági mentési házirendek egy adott adatbázis beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Hozzon létre vagy egy adatbázis geobackup szabályzat frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Rendelkezésre álló karbantartási időszakok listájának lekérése a kiválasztott adatbázishoz. |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindows/read | Karbantartási lekérdezi a windows-beállítások a kiválasztott adatbázishoz. |
> | Műveletek | Microsoft.Sql/servers/databases/maintenanceWindows/write | Karbantartási beállítja a windows-beállítások a kiválasztott adatbázishoz. |
> | Műveletek | Microsoft.Sql/servers/databases/metricDefinitions/read | Návratové typy metrikák elérhető adatbázisok |
> | Műveletek | Microsoft.Sql/servers/databases/metrics/read | Az adatbázisok visszatérési metrikák |
> | Műveletek | Microsoft.Sql/servers/databases/move/action | Nevezze át az Azure SQL Database |
> | Műveletek | Microsoft.Sql/servers/databases/operationResults/read | Adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/cancel/action | Azure SQL Database megszakítja a függőben lévő aszinkron művelet, amely még nem fejeződött be. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/read | A listát az adatbázison végrehajtott műveletek |
> | Műveletek | Microsoft.Sql/servers/databases/pause/action | Az Azure SQL Datawarehouse-adatbázis szüneteltetése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Adatbázisok számára elérhető naplók beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrikák elérhető adatbázisok |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | A gyűjtemény lekérdezési szövegek, amelyek megfelelnek a megadott paraméterekkel adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/read | A Query Store az adatbázis beállításainak aktuális értékeit adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/write | Az adatbázis a Query Store beállítás frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/delete | A replikációs kapcsolat le kellett zárnia és a lehetséges adatvesztéssel |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Feladatátvételi minden szinkronizálás után módosítja az elsődleges kiszolgálóról a adatbázis tétele a replikációs relationship\u0027s az elsődleges és a a távoli elsődleges, másodlagos |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | A replikációs relationship\u0027s be az adatbázis elsődleges tétele és a távoli elsődleges be egy másodlagos azonnal az esetleges adatvesztés feladatátvétel |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/read | A replikációs hivatkozások vagy tulajdonságainak beolvasása a megadott replikációs hivatkozáshoz tartozó visszaadása. |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Megszünteti a replikációs kapcsolat kényszerített vagy, a partnerrel való szinkronizálás után |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Hivatkozás replikációs mód szinkron vagy aszinkron módban való frissítéséhez |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/action | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/delete | Az adatbázis visszaállítási pont törlése. |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/read | A visszaállítási pontok az adatbázis értéket ad vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/resume/action | Az Azure SQL Datawarehouse-adatbázis folytatása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/read | Sémák adatbázisok listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy tábla oszlopait listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Az érzékenységi címkét egy adott oszlop törlése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop a bizalmassági címke beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Létrehozni vagy frissíteni a érzékenységi címkét egy adott oszlop |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/read | Táblák adatbázisok listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Ajánlott indexek az adatbázisok listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | A alkalmazni indexjavaslatot |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/read | A konfigurált egy adott adatbázis fenyegetésészlelési szabályzatát részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/write | A megadott adatbázis fenyegetésészlelési szabályzatát módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/securityMetrics/read | Adatbázis biztonsági mérőszámból álló gyűjtemény beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkék felsorolása |
> | Műveletek | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Vissza az adatbázis kiterjesztése vagy szűkítése kapcsolatos javaslat teljesítményének javítása és a költségek csökkentése lekérdezés-végrehajtási statisztikák alapján |
> | Műveletek | Microsoft.Sql/servers/databases/skus/read | Lekéri egy adatbázis elérhető termékváltozatok gyűjteménye |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Szinkronizálási csoport szinkronizálási megszakítása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/delete | Meglévő szinkronizálási csoport törlése. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | A szinkronizálási listáját hub adatbázissémák visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/logs/read | A szinkronizálási csoport naplók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/read | Vissza a szinkronizálási csoportok, vagy a megadott szinkronizálási csoport tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Szinkronizáló központ adatbázisséma frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | A szinkronizálási hub séma frissítési művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Töröl egy meglévő szinkronizálási tag. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Szinkronizálási tagok vagy lekérdezi a tulajdonságok listájának visszaadása a megadott szinkronizálási tag. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | A szinkronizálási tag séma frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | A szinkronizálási tag séma frissítési művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | A szinkronizálási listáját tag adatbázissémák visszaadása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | A szinkronizálási tag a megadott paraméterekkel létrehozása vagy frissítése a tulajdonságok a megadott szinkronizálási tag számára. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Az eseményindító szinkronizálási csoport szinkronizálási |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/write | Szinkronizálási csoport létrehozása a megadott paraméterekkel, vagy a szinkronizálási csoport tulajdonságok frissítésére. |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/queryText/action | A Transact-SQL szöveget adja vissza, a kijelölt lekérdezés azonosítója |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/read | Értéket ad vissza a kijelölt időszakban összesítve a kiválasztott lekérdezés futásidejű statisztikája |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/statistics/read | Értéket ad vissza a kijelölt időszakban összesítve a kiválasztott lekérdezés futásidejű statisztikája |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Lekérdezi a folyamatban lévő műveleteket a transzparens adattitkosítás |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Állapotával és részleteivel kapcsolatban egy adott adatbázis transzparens titkosítási biztonsági funkció lekéréséhez |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Transzparens titkosítási állapot módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Az Azure SQL Datawarehouse-adatbázis frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/usages/read | Az Azure SQL Database a tanúsítványalgoritmusok információkat kér le |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | A biztonságirés-értékelési egy adott adatbázis eltávolítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Egy adott adatbázisban konfigurált biztonságirés-értékelési részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | A sebezhetőségi felmérés szabály alapkonfigurációja egy adott adatbázis eltávolítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | A sebezhetőségi felmérés szabály alapkonfigurációja beolvasása egy adott adatbázishoz |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | A sebezhetőségi felmérés szabály alapkonfigurációja egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Egy meglévő vizsgálati eredménynek emberi olvasható formátumba konvertálja. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Biztonságirés-értékelési adatbázis vizsgálat végrehajtása. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Adatbázis biztonsági rések listáját értékelési vizsgálat rekordot ad vissza, vagy a vizsgálat rekord lekérése a megadott vizsgálat azonosítóját. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | A biztonságirés-értékelési egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Biztonságirés-értékelési adatbázis vizsgálat végrehajtása. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Az adatbázis biztonsági réseinek kiértékelési vizsgálata Execute művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Egy adott adatbázisban konfigurált biztonságirés-értékelési részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | A biztonságirés-értékelési egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/write | Létrehoz egy adatbázist a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a meghatározott adatbázis frissítése. |
> | Műveletek | Microsoft.Sql/servers/delete | Töröl egy meglévő kiszolgálóra. |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Töröl egy meglévő vész-helyreállítási beállítások egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Feladatátvétel a DisasterRecoveryConfiguration |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Egy DisasterRecoveryConfiguration feladatátvételt kényszerít ki |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Vészhelyreállítási gyűjteménye lekéri helyreállítási olyan konfigurációkat, amelyek ehhez a kiszolgálóhoz |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Kiszolgálók vészhelyreállítási konfigurációs módosítás |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/read | Ehhez a kiszolgálóhoz már létrehozott rugalmas készlet becsült listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/write | Új rugalmas készlet becsült értéke a megadott adatbázisok listájának létrehozása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/read | A rugalmas készlet esetében elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | A megadott tanácsadó a rugalmas készlet esetében ajánlott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | A javasolt műveleteket alkalmazhat a rugalmas készlet |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/write | Frissítés Automatikus végrehajtás-tanácsadó a rugalmas készlet szinten állapotát. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/databases/read | Rugalmas készletek az adatbázisok listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/delete | Meglévő rugalmas készlet törlése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Tevékenységek és a egy adott rugalmas adatbáziskészlet információt lekérni |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Tevékenységek és a egy adott adatbázishoz rugalmas adatbáziskészlet részét képező információt lekérni |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Návratové typy metrikák elérhető rugalmas adatbáziskészletek |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metrics/read | Rugalmas adatbáziskészletek visszatérési metrikák |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Megszakítja az Azure SQL rugalmas készlet, amely még nem fejeződött be aszinkron művelet függőben van. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/read | A rugalmas készlet végrehajtott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrikák elérhető rugalmas adatbáziskészletek |
> | Műveletek | Microsoft.Sql/servers/elasticPools/read | Rugalmas készlet a megadott kiszolgálón részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/skus/read | Rugalmas készletek gyűjteménye, rendelkezésre álló termékváltozatok beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/write | Hozzon létre egy új vagy meglévő rugalmas készlet tulajdonságainak módosítása |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/read | Kiszolgáló titkosítási protectors listáját adja vissza, vagy a Tulajdonságok lekérdezi a megadott kiszolgáló titkosítási védelme. |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/write | A tulajdonságainak frissítése az a megadott kiszolgáló titkosítási védelme. |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/read | A naplózás a megadott kiszolgálón konfigurált szabályzatot a kiterjesztett kiszolgáló BLOB részleteinek lekérése |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/write | A kiterjesztett kiszolgálószintű blobnaplózás egy adott kiszolgálóhoz módosítása |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/delete | Egy meglévő feladatátvételi csoport törlése. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/failover/action | Tervezett feladatátvétel végrehajtása létező feladatátvételi csoportba. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvétel végrehajtása egy már meglévő feladatátvételi csoportban. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/read | A lista a feladatátvételi csoportok, vagy a megadott feladatátvételi csoport tulajdonságainak lekérése adja vissza. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/write | Egy feladatátvételi csoportot hoz létre a megadott paramétereket, vagy a Tulajdonságok vagy címkék a megadott feladatátvételi csoport frissítése. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/delete | Törli a kiszolgáló meglévő tűzfalszabály. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/read | Kiszolgálótűzfal listája szabályokat, vagy a megadott kiszolgáló tulajdonságainak lekérése tűzfalszabály vissza. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/write | Egy kiszolgálói tűzfalszabályt hoz létre a megadott paraméterekkel, a megadott szabály tulajdonságainak frissítéséhez, vagy minden meglévő szabály felülírja az új kiszolgáló tűzfal szabály(ok). |
> | Műveletek | Microsoft.Sql/servers/import/action | Hozzon létre egy új adatbázist a kiszolgálón, és üzembe helyezése a séma- és egy adatrétegbeli alkalmazáscsomagot csomag adatait |
> | Műveletek | Microsoft.Sql/servers/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Műveletek | Microsoft.Sql/servers/keys/delete | Meglévő kiszolgáló kulcs törlése. |
> | Műveletek | Microsoft.Sql/servers/keys/read | Vissza a kiszolgáló a kulcsok vagy a kiszolgáló megadott kulcs tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Sql/servers/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott kiszolgáló kulcs frissítése. |
> | Műveletek | Microsoft.Sql/servers/networkInterfaces/read | A megadott hálózati adapter tulajdonságokat adja vissza |
> | Műveletek | Microsoft.Sql/servers/networkInterfaces/write | Létrehoz egy hálózati adaptert a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott hálózati adapter frissítése |
> | Műveletek | Microsoft.Sql/servers/operationResults/read | Lekérdezi a folyamatban lévő műveletekhez |
> | Műveletek | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy mérőszámok rendelkezésre álló kiszolgálók |
> | Műveletek | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgáló tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Egy adott kiszolgálóhoz ajánlott rugalmas adatbáziskészletek metrikák beolvasása |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/read | Rugalmas adatbáziskészletek költségcsökkentés és teljesítményjavítás historica erőforrás-használat alapján javasolt beolvasása |
> | Műveletek | Microsoft.Sql/servers/recoverableDatabases/read | A művelettel élő adatbázis vész-helyreállítási adatbázis visszaállítása az utolsó ismert helyes biztonsági mentési pontra. Információt ad vissza, de a legutóbbi helyes biztonsági mentés kapcsolatos doesn\u0027t ténylegesen állítsa vissza az adatbázist. |
> | Műveletek | Microsoft.Sql/servers/replicationLinks/read | A replikációs hivatkozások vagy tulajdonságainak beolvasása a megadott replikációs hivatkozáshoz tartozó visszaadása. |
> | Műveletek | Microsoft.Sql/servers/restorableDroppedDatabases/read | Adatbázisok, amelyek el lettek dobva a megadott kiszolgálón belül adatmegőrzési listájának beolvasása. |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | A kiszolgáló írási threat detection szabályzatművelet eredményeinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/read | A kiszolgáló fenyegetésészlelési szabályzatát a megadott kiszolgálón konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/write | A kiszolgáló egy adott kiszolgálóhoz fenyegetésészlelési szabályzatát módosítása |
> | Műveletek | Microsoft.Sql/servers/serviceObjectives/read | Szolgáltatásiszint-célkitűzések (más néven teljesítményszintek) a megadott kiszolgálón elérhető listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/syncAgents/delete | Törli a meglévő szinkronizálási ügynök. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/generateKey/action | Szinkronizálási ügynök registeration kulcs generálása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Szinkronizálási ügynök társított adatbázisok listájának visszaadása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/read | Szinkronizációs ügynökök vagy lekérdezi a tulajdonságok listájának visszaadása a megadott szinkronizálási ügynök. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/write | A szinkronizálási ügynök hoz létre a megadott paraméterek vagy a megadott szinkronizálási ügynök tulajdonságainak frissítése. |
> | Műveletek | Microsoft.Sql/servers/tdeCertificates/action | Tanúsítvány létrehozása vagy frissítése TDE |
> | Műveletek | Microsoft.Sql/servers/usages/read | A kiszolgálón található összes adatbázis a kiszolgáló DTU-kvótával és az aktuális DTU consuption vissza |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/delete | Törli a meglévő virtuális hálózati szabály |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/read | Vissza a virtuális hálózati szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/write | Egy virtuális hálózati szabályt hoz létre a megadott paramétereket, vagy a Tulajdonságok vagy címkék a megadott virtuális hálózati szabály frissítése. |
> | Műveletek | Microsoft.Sql/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy a Tulajdonságok vagy címkék a megadott kiszolgáló frissítése. |
> | Műveletek | Microsoft.Sql/unregister/action | Az előfizetés a Microsoft SQL Database erőforrás-szolgáltató regisztrációjának törlése, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft.Sql/virtualClusters/read | A virtuális fürtök vagy lekérdezi a megadott virtuális fürt tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/virtualClusters/write | Virtuális fürt címkék frissíti. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Storage/checknameavailability/read | Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban. |
> | Műveletek | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban |
> | Műveletek | Microsoft.Storage/locations/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |
> | Műveletek | Microsoft.Storage/operations/read | Az aszinkrón műveletek állapotának lekérdezése. |
> | Műveletek | Microsoft.Storage/register/action | A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése. |
> | Műveletek | Microsoft.Storage/skus/read | A Microsoft.Storage által támogatott termékváltozatok listázása. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | A blobtartalom hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | A blob parancs eredményének visszaadása |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blobtároló visszatartásának törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blobtároló módosíthatatlansági szabályzatának törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blobtároló bővítésére vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blobtároló zárolására vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blobtároló beolvasására vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blobtárolón végrehajtott put műveletre vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blobtároló visszatartásának beállítása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/write | A put vagy lease blobtároló-művelet eredményének visszaadása |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználói delegálási kulcsot ad vissza a Blob service szolgáltatáshoz |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/logDefinitions/read | Blob naplódefiníciójának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/read | A Blob szolgáltatás tulajdonságait vagy a statisztikát adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/write | A blobszolgáltatás tulajdonságain végrehajtott Put művelet eredményének visszaadása |
> | Műveletek | Microsoft.Storage/storageAccounts/delete | Meglévő tárfiók törlése. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/fileShare/delete | Engedélyezi a felhasználónak a fájlmegosztások törlését |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/fileShare/read | Engedélyezi a felhasználónak fájlmegosztások olvasását |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/fileShare/write | Engedélyezi a felhasználónak a fájlmegosztásokra való írást |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/logDefinitions/read | Fájl naplódefiníciójának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/listAccountSas/action | A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Műveletek | Microsoft.Storage/storageAccounts/listServiceSas/action | A szolgáltatás SAS-jogkivonatát adja vissza a meghatározott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/logDefinitions/read | Üzenetsor naplódefiníciójának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Sor törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Üzenet törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet feldolgozásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/write | A sor írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/read | A Queue szolgáltatás tulajdonságait vagy a statisztikát adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/write | A Queue szolgáltatás tulajdonságai beállításának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Tárfiók diagnosztikai beállításainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/logDefinitions/read | Tábla naplódefiníciójának beolvasása |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/write | Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.storagesync/locations/checkNameAvailability/action | A Társzinkronizálási szolgáltatás szolgáltatásnév elérhetőségének ellenőrzése |
> | Műveletek | microsoft.storagesync/storageSyncServices/delete | Bármely Társzinkronizálási szolgáltatás törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | A Társzinkronizálási szolgáltatás elérhető metrikáinak lekérése |
> | Műveletek | microsoft.storagesync/storageSyncServices/read | Olvassa el a bármely Társzinkronizálási szolgáltatás |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/delete | Minden regisztrált kiszolgáló törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | A regisztrált Server elérhető metrikáinak lekérése |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/read | Olvassa el a minden regisztrált kiszolgáló |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/write | Létrehozás vagy frissítés bármely regisztrált kiszolgáló |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Bármely Felhőbeli végpont törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hely api aszinkron biztonsági mentési hívások |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Biztonsági mentés után ez a művelet meghívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Visszaállítás után ez a művelet meghívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Biztonsági mentés előtt. Ez a művelet meghívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Ez a művelet előtt visszaállítási hívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Olvassa el az összes Felhőbeli végpontot |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Szívverés visszaállítása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Hozzon létre vagy bármely Felhőbeli végpont frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/delete | Szinkronizálási csoportok törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Szinkronizálási csoportok számára elérhető metrikáinak lekérése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/read | Olvassa el a szinkronizálási csoportokkal |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Törölje minden olyan kiszolgálói végpontot |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | A Kiszolgálóvégpontok elérhető metrikáinak lekérése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Olvassa el az összes kiszolgálói végpontot |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Ez a művelet egy visszahívása hívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Létrehozás vagy frissítés bármely kiszolgálói végpontot |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/write | Minden olyan szinkronizálási csoportok létrehozása vagy frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/write | Létrehozásához vagy frissítéséhez bármely Társzinkronizálási szolgáltatás |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/delete | Hozzáférés-vezérlési bejegyzés törlése |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/read | Sorolja fel, vagy a hozzáférés-vezérlési rekordok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/write | Létrehozni vagy frissíteni a hozzáférés-vezérlési rekordok |
> | Műveletek | Microsoft.StorSimple/managers/alerts/read | Sorolja fel, vagy a riasztások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/backups/read | Sorolja fel, vagy lekérdezi a biztonságimásolat-készlet |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/delete | Töröl egy meglévő sávszélesség-beállítások (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/read | A sávszélesség-beállítások listája (csak a 8000-es sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/write | Létrehoz egy új, vagy a sávszélesség-beállítások frissítése (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/certificates/write | A tanúsítványok létrehozása vagy módosítása |
> | Műveletek | Microsoft.StorSimple/Managers/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tár hitelesítőadat-tanúsítványa. |
> | Műveletek | Microsoft.StorSimple/managers/clearAlerts/action | Törölje a jelet az Eszközkezelő társított riasztásait. |
> | Műveletek | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | A lista a felhőalapú berendezés által támogatott konfigurációk |
> | Műveletek | Microsoft.StorSimple/managers/configureDevice/action | Eszközök konfigurálása |
> | Műveletek | Microsoft.StorSimple/managers/delete | Törli a Eszközkezelők |
> | Műveletek | Microsoft.StorSimple/Managers/delete | A tár törlése művelettel törölhetők a megadott Azure-erőforrás "tároló" típusú |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/read | Sorolja fel, vagy lekérdezi a riasztási beállítások |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/write | Hozzon létre vagy a riasztási beállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | A szolgáltatási adattitkosítási kulcshoz kapcsolódó kulcsváltás eszközök engedélyezése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Manuális biztonsági mentés létrehozása egy igény szerinti biztonsági mentés, a házirend által védett összes kötetről. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Töröl egy meglévő biztonsági mentési házirendek (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/read | Lista a biztonsági mentési házirendek (csak a 8000-es sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Egy meglévő ütemezés törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Az ütemezések listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Létrehoz egy új, vagy az ütemezések frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/write | Létrehoz egy új, vagy frissíti a biztonsági mentési házirendek (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/delete | A biztonságimásolat-készlet törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klónozza a megosztásra vagy egy biztonsági mentési elem használatával. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/read | Sorolja fel, vagy lekérdezi a biztonságimásolat-készlet |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/restore/action | Állítsa vissza minden kötet a biztonságimásolat-készletből. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | A biztonsági mentési ütemezés csoportok törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Sorolja fel, vagy a biztonsági mentési ütemezés csoportok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | A biztonsági mentési ütemezés csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/delete | A Chap-beállítások törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/read | Sorolja fel, vagy a Chap-beállítások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/write | Létrehozni vagy frissíteni a Chap-beállítások |
> | Műveletek | Microsoft.StorSimple/managers/devices/deactivate/action | Inaktiválja az eszközt. |
> | Műveletek | Microsoft.StorSimple/managers/devices/delete | Az eszközök törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/disks/read | Sorolja fel, vagy lekérdezi a lemezek |
> | Műveletek | Microsoft.StorSimple/managers/devices/download/action | Egy eszköz frissítések letöltéséhez. |
> | Műveletek | Microsoft.StorSimple/managers/devices/failover/action | Az eszköz feladatátvétele. |
> | Műveletek | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/failoverTargets/read | Sorolja fel, vagy a feladatátvételi célt, az eszközök beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Biztonsági mentés fájlkiszolgálók. |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/delete | Törli a fájlkiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/read | Sorolja fel, vagy lekérdezi a fájlkiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Törli a megosztásokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Sorolja fel, vagy lekérdezi a megosztásokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Létrehozni vagy frissíteni a megosztásokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/write | Létrehozni vagy frissíteni a fájlkiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Módosítsa a vezérlő energiaállapotát az összetevőcsoportok hardver |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | A hardver összetevő csoportok listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/install/action | Frissítések telepítése az eszközön. |
> | Műveletek | Microsoft.StorSimple/managers/devices/installUpdates/action | Telepíti a frissítéseket az eszközökre (8000-es sorozat csak). |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Biztonsági mentés az iSCSI-kiszolgálót. |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Az iSCSI-kiszolgáló törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | A lemez törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Sorolja fel, vagy lekérdezi a lemezek |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Létrehozni vagy frissíteni a lemezt |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/read | Sorolja fel, vagy az iSCSI-tároló kiszolgálók beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/write | Hozzon létre vagy az iSCSI-kiszolgáló frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Egy futó feladat megszakítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/read | Sorolja fel, vagy a feladatok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Egy meglévő eszközt (8000-es sorozat csak) feladatátvételi készleteinek listája. |
> | Műveletek | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Feladatátvételi célt (8000-es sorozat csak) az eszközök listája. |
> | Műveletek | Microsoft.StorSimple/managers/devices/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Megerősíti a sikeres áttelepítéshez, és véglegesítse azt. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista a Migrálás állapota megerősítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Az áttelepítés megerősítése állapotát olvassa be. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | A migrálási felmérést feladat állapotának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Az áttelepítés állapotát olvassa be. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Az áttelepítéshez forrás konfigurációk importálása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | A Migrálás becslés listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | A Migrálás állapota listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Indítsa el áttelepítési adatforrás-konfiguráció használata |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Az áttelepítési folyamat időtartamának becsült feladat elindításához. |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/read | Sorolja fel, vagy a hálózati beállítások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/write | Létrehoz egy új, vagy a hálózati beállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Az Eszközkezelő lista nyilvános titkosítási kulcsa |
> | Műveletek | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Támogatási csomag Support hibaelhárítási eszköz közzététele. |
> | Műveletek | Microsoft.StorSimple/managers/devices/read | Sorolja fel, vagy az eszközök beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Az eszköz a frissítések keresése. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/read | A biztonsági beállítások listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | A távfelügyeleti tanúsítvány eszköz szinkronizálása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/update/action | A biztonsági beállítások frissítése. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/write | Létrehoz egy új, vagy a biztonsági beállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Tesztriasztási e-mail küldése a beállított e-mail-címzettek. |
> | Műveletek | Microsoft.StorSimple/managers/devices/shares/read | Sorolja fel, vagy lekérdezi a megosztásokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/read | Sorolja fel, vagy lekérdezi az időbeállítások |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/write | Létrehoz egy új, vagy időbeállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/updateSummary/read | Sorolja fel, vagy a frissítési összegzés beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Töröl egy meglévő Kötettárolók (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | A mérőszámok listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | A metrikai meghatározások listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/read | A Kötettárolók listája (csak a 8000-es sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Töröl egy meglévő kötetek |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | A mérőszámok listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | A metrikai meghatározások listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | A művelet eredményei listázása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | A kötetek listán |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Létrehoz egy új, vagy a kötet frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/write | Létrehoz egy új, vagy frissíti a Kötettárolók (8000-es sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumes/read | A kötetek listán |
> | Műveletek | Microsoft.StorSimple/managers/devices/write | Hozzon létre, vagy frissítheti az eszközeit |
> | Műveletek | Microsoft.StorSimple/managers/encryptionSettings/read | Sorolja fel, vagy a titkosítási beállítások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/delete | Törli a kiterjesztett Táradatokat |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/read | Sorolja fel, vagy lekérdezi a kiterjesztett Táradatokat |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/write | A kiterjesztett tároló adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/managers/features/read | A szolgáltatások listája |
> | Műveletek | Microsoft.StorSimple/managers/fileservers/read | Sorolja fel, vagy lekérdezi a fájlkiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/getActivationKey/action | A kezelő aktiválási kulcs beszerzése. |
> | Műveletek | Microsoft.StorSimple/managers/getEncryptionKey/action | A kezelő titkosítási kulcs beszerzése. |
> | Műveletek | Microsoft.StorSimple/managers/iscsiservers/read | Sorolja fel, vagy az iSCSI-tároló kiszolgálók beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/jobs/read | Sorolja fel, vagy a feladatok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/listActivationKey/action | Az aktiválási kulcsot a StorSimple-Eszközkezelő beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | A StorSimple-Eszközkezelő nyilvános titkosítási kulcsainak listázása. |
> | Műveletek | Microsoft.StorSimple/managers/metrics/read | Sorolja fel, vagy metrikáinak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/metricsDefinitions/read | Sorolja fel, vagy a metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Áttelepítése klasszikus Resource Manager-Mangers |
> | Műveletek | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Az áttelepítés forrás konfigurációk listája (csak a 8000-es sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Hozzon létre egy új felhőalapú berendezést. |
> | Műveletek | Microsoft.StorSimple/managers/read | Sorolja fel, vagy lekérdezi a Eszközkezelők |
> | Műveletek | Microsoft.StorSimple/Managers/read | A tároló Get művelet lekérdezi egy Azure-erőforrásokra "tároló" típusú objektum |
> | Műveletek | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Generálja újra a regisztrációs tanúsítvány eszközkezelők számára. |
> | Műveletek | Microsoft.StorSimple/managers/regenerateActivationKey/action | A kezelő aktiválási kulcs újragenerálása. |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/delete | A Tárfiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/read | Sorolja fel, vagy a Tárfiók hitelesítő adatainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/write | Hozzon létre vagy a Tárfiók hitelesítő adatainak frissítése |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/delete | Törli a Storage-tartományok |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Sorolja fel, vagy a művelet eredményeinek beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/read | Sorolja fel, vagy lekérdezi a Tárhelytartományok |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/write | Hozzon létre vagy a Storage-tartományok frissítése |
> | Műveletek | Microsoft.StorSimple/managers/write | Létrehozás vagy frissítés Eszközkezelők |
> | Műveletek | Microsoft.StorSimple/Managers/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Műveletek | Microsoft.StorSimple/operations/read | Sorolja fel, vagy a műveletek beolvasása |
> | Műveletek | Microsoft.StorSimple/register/action | Regisztrálja a szolgáltatót Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StreamAnalytics/locations/quotas/Read | Olvasási Stream Analytics-előfizetési kvóta |
> | Műveletek | Microsoft.StreamAnalytics/operations/Read | Olvasási Stream Analytics-műveletet |
> | Műveletek | Microsoft.StreamAnalytics/Register/action | Előfizetés regisztrálása a Stream Analytics erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics-feladat törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Törölje a Stream Analytics-feladat függvénye |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | A Stream Analytics-feladat függvénye műveleti eredmények beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Olvasási Stream Analytics-feladat függvénye |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | A Stream Analytics-feladat függvénye alapértelmezett definíciójának lekérése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Teszt Stream Analytics-feladat függvénye |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Írás a Stream Analytics-feladat függvénye |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Törölje a Stream Analytics-feladat bemenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | A Stream Analytics-feladat bemenete műveleti eredmények beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Olvasási Stream Analytics-feladat bemenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Minta Stream Analytics-feladat bemenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Teszt Stream Analytics-feladat bemenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Írás a Stream Analytics-feladat bemenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | A Metrikadefiníciók olvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics-feladat műveleti eredmények beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Törölje a Stream Analytics-feladat kimenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Műveleti eredmények beolvasása a Stream Analytics-feladat kimenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Olvasási Stream Analytics-feladat kimenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Teszt Stream Analytics-feladat kimenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Írás a Stream Analytics-feladat kimenete |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítás beolvasása. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítás írása. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Streamingjobs naplók beolvasása. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Streamingjobs elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics-feladat olvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics-feladat indítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics-feladat leállítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Törölje a Stream Analytics-feladat transzformációja |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Olvasási Stream Analytics-feladat transzformációja |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Írás a Stream Analytics-feladat transzformációja |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Write | Írás a Stream Analytics-feladat |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/read | A felügyeleti csoporton belül az Azure-előfizetés-definíció beolvasása. |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/write | Azure-előfizetés definíció létrehozása |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra. |
> | Műveletek | Microsoft.Support/supportTickets/read | Támogatási jegy részleteinek lekérése (beleértve az állapotot, súlyosságot, kapcsolattartási adatokat és kommunikációkat) vagy az összes előfizetés támogatási jegyeinek lekérése. |
> | Műveletek | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | A hozzáférési házirend törlése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hozzáférési házirend tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Létrehoz egy új hozzáférési szabályzat egy adott környezetben, vagy frissíti egy meglévő hozzáférési szabályzatot. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/delete | A környezet törlése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Törli az esemény forrását. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Eventsources elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/read | Eseményforrás tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/write | Létrehoz egy új eseményforrás egy adott környezetben, vagy egy meglévő eseményforrás frissítése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Környezetekben elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/read | A környezet tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Törli a referencia-adatkészlet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Referencia-adatkészlet tulajdonságait olvassa be. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Létrehoz egy új referencia-adatkészlet egy adott környezetben, vagy egy meglévő referencia-adatkészlet frissítése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/status/read | A környezet állapotát, a kapcsolódó műveleteket, például a bejövő forgalom állapotának beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/write | Létrehoz egy új környezetet, vagy egy meglévő környezet frissítése. |
> | Műveletek | Microsoft.TimeSeriesInsights/register/action | A Time Series Insights erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása, és lehetővé teszi a Time Series Insights-környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Műveletek | Microsoft.VisualStudio/Account/Extension/Read | Olvassa el a fiók/bővítmény |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Read | Olvassa el a fiókot, projektgyűjtemény |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Write | Fiók és a projekt beállítása |
> | Műveletek | Microsoft.VisualStudio/Account/Read | Fiók beolvasása |
> | Műveletek | Microsoft.VisualStudio/Account/Write | Fiók beállítása |
> | Műveletek | Microsoft.VisualStudio/Extension/Delete | Bővítmény törlése |
> | Műveletek | Microsoft.VisualStudio/Extension/Read | Olvassa el a bővítményt |
> | Műveletek | Microsoft.VisualStudio/Extension/Write | Bővítmény beállítása |
> | Műveletek | Microsoft.VisualStudio/Project/Delete | Projekt törlése |
> | Műveletek | Microsoft.VisualStudio/Project/Read | Olvassa el a projekt |
> | Műveletek | Microsoft.VisualStudio/Project/Write | Projekt beállítása |
> | Műveletek | Microsoft.VisualStudio/Register/Action | Az Azure-előfizetés regisztrálása Microsoft.VisualStudio szolgáltatóval |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Web/apimanagementaccounts/apiacls/Read | Api Management-fiókok Apiacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Api Management-fiókok API-k Apiacls törlése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Api Management-fiókok API-k Apiacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Api Management-fiókok API-k Apiacls frissítése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Api Management-fiókok API-k Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Erősítse meg a jóváhagyási kód Api-felügyeleti fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Api Management fiókok API-k kapcsolatok Connectionacls törlése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Api Management fiókok API-k kapcsolatok Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Api Management fiókok API-k kapcsolatok Connectionacls frissítése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Api Management-fiókok API-k kapcsolatok törlése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Szerezze be a jóváhagyási hivatkozásainak az Api Management-fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Lista kapcsolat kulcsok API-t felügyeleti fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista titkos Api felügyeleti fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Api Management-fiókok API-k kapcsolatok lekérése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Frissítse az Api Management-fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Api Management-fiókok API-k törlése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Törli az Api Management fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Az Api Management első fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Frissítés Api Management fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Read | Api Management-fiókok API-k lekérése. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Write | Frissítse az Api Management-fiókok API-kat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Api Management-fiókok Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/availablestacks/Read | Első elérhető implementációt. |
> | Műveletek | Microsoft.Web/billingmeters/Read | Számlázási mérőszámokat tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Web/certificates/Delete | Egy meglévő tanúsítvány törlése. |
> | Műveletek | Microsoft.Web/certificates/Read | Tanúsítványok listájának beolvasása. |
> | Műveletek | Microsoft.Web/certificates/Write | Adjon hozzá egy új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Műveletek | Microsoft.Web/checknameavailability/Read | Ellenőrizze az erőforrás neve elérhető-e. |
> | Műveletek | Microsoft.Web/classicmobileservices/Read | Klasszikus Mobile Services beszerzése. |
> | Műveletek | Microsoft.Web/connectionGateways/Delete | A kapcsolódási átjáró törlése. |
> | Műveletek | Microsoft.Web/connectionGateways/Join/Action | A kapcsolódási átjáró csatlakozik. |
> | Műveletek | Microsoft.Web/connectionGateways/ListStatus/Action | A kapcsolódási átjáró állapotának listája. |
> | Műveletek | Microsoft.Web/connectionGateways/Move/Action | A kapcsolódási átjáró helyezi. |
> | Műveletek | Microsoft.Web/connectionGateways/Read | Kapcsolat átjárók listájának beolvasása. |
> | Műveletek | Microsoft.Web/connectionGateways/Write | Létrehozza vagy frissíti a kapcsolódási átjáró. |
> | Műveletek | Microsoft.Web/Connections/confirmconsentcode/Action | Erősítse meg a kapcsolatok jóváhagyási kódot. |
> | Műveletek | Microsoft.Web/connections/Delete | Törli a kapcsolatot. |
> | Műveletek | Microsoft.Web/connections/Join/Action | A kapcsolat csatlakozik. |
> | Műveletek | Microsoft.Web/Connections/listconsentlinks/Action | Kapcsolatok listája jóváhagyási hivatkozásokat. |
> | Műveletek | Microsoft.Web/connections/Move/Action | A kapcsolat helyezi. |
> | Műveletek | Microsoft.Web/connections/Read | A kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft.Web/connections/Write | Létrehozza vagy frissíti a kapcsolatot. |
> | Műveletek | Microsoft.Web/customApis/Delete | Törli az egyéni API-t. |
> | Műveletek | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | API-definíció kiolvassa a WSDL-leírásra MUTAT. |
> | Műveletek | Microsoft.Web/customApis/Join/Action | Egyéni API-k csatlakozik. |
> | Műveletek | Microsoft.Web/customApis/listWsdlInterfaces/Action | Egyéni API WSDL-felületek sorolja fel. |
> | Műveletek | Microsoft.Web/customApis/Move/Action | Egyéni API-k helyezi. |
> | Műveletek | Microsoft.Web/customApis/Read | Egyéni API-t listájának beolvasása. |
> | Műveletek | Microsoft.Web/customApis/Write | Létrehozza vagy frissíti egy egyéni API-t. |
> | Műveletek | Microsoft.Web/deletedSites/Read | Törölt webalkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/deploymentlocations/Read | Beolvasni a központi telepítési helyét. |
> | Műveletek | Microsoft.Web/geoRegions/Read | A földrajzi régiók listájának beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/capacities/Read | Első üzemeltetési környezetek kapacitások. |
> | Műveletek | Microsoft.Web/hostingEnvironments/Delete | App Service-környezet törlése |
> | Műveletek | Microsoft.Web/hostingenvironments/detectors/Read | Első üzemeltetési környezetek derítik fel. |
> | Műveletek | Microsoft.Web/hostingenvironments/Diagnostics/Read | Első üzemeltetési környezetek diagnosztika. |
> | Műveletek | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Az összes bejövő függőségek a hálózati végpont lekérése. |
> | Műveletek | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Első üzemeltetési környezetek Metrikadefinícióinak beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Első üzemeltetési környezetek többcélú készletek Metrikadefinícióinak beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Első üzemeltetési környezetek többcélú készletek metrikákat. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet MultiRole elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Read | App Service Environment-környezetben előtérbeli címkészlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Első üzemeltetési környezetek többcélú készletek termékváltozatokat. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Első üzemeltetési környezetek többcélú készletek Használatokban. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Hozzon létre egy új előtérbeli címkészletet App Service Environment-környezetben, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/hostingenvironments/Operations/Read | Első üzemeltetési környezetek műveleteket. |
> | Műveletek | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | A hálózati végpontok összes kimenő függőségi beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/hostingEnvironments/Read | App Service-környezet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingEnvironments/reboot/Action | Az App Service-környezet összes gép újraindítása |
> | Műveletek | Microsoft.Web/hostingenvironments/Resume/Action | Folytatódik a üzemeltetési környezetek. |
> | Műveletek | Microsoft.Web/hostingenvironments/serverfarms/Read | Első üzemeltetési környezetek App Service-csomagok. |
> | Műveletek | Microsoft.Web/hostingenvironments/Sites/Read | Első üzemeltetési környezetek webalkalmazásokat. |
> | Műveletek | Microsoft.Web/hostingenvironments/suspend/Action | Felfüggesztheti az üzemeltetési környezetek. |
> | Műveletek | Microsoft.Web/hostingenvironments/usages/Read | Első üzemeltetési környezetek Használatokban. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Környezetek Workerpools Metrikadefiníciók beolvasása üzemeltetéséhez. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Első üzemeltetési környezetek Workerpools metrikákat. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet WorkerPool elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Read | App Service Environment-környezetben a Feldolgozókészletek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Első üzemeltetési környezetek Workerpools termékváltozatokat. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Első üzemeltetési környezetek Workerpools Használatokban. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Write | Hozzon létre egy új Feldolgozókészlet App Service Environment-környezetben, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/hostingEnvironments/Write | Egy új App Service Environment-környezet létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Web/ishostingenvironmentnameavailable/Read | GET, ha az üzemeltetési környezet neve érhető el. |
> | Műveletek | Microsoft.Web/ishostnameavailable/Read | Ellenőrizze, hogy a Hostname érhető el. |
> | Műveletek | Microsoft.Web/isusernameavailable/Read | Ellenőrizze, hogy a felhasználónév érhető el. |
> | Műveletek | Microsoft.Web/listSitesAssignedToHostName/Read | Lekérése az állomásnév rendelt helyek nevét. |
> | Műveletek | Microsoft.Web/Locations/apioperations/Read | Helyek API műveletek beolvasása. |
> | Műveletek | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Első helyek kapcsolódási átjáró telepítését. |
> | Műveletek | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Api-definíció kibontása WSDL-fájlból helyeket. |
> | Műveletek | Microsoft.Web/Locations/listwsdlinterfaces/Action | Lista WSDL felületek helyeket. |
> | Műveletek | Microsoft.Web/Locations/managedapis/apioperations/Read | Helyek felügyelt API-műveletek lekérése. |
> | Műveletek | Microsoft.Web/locations/managedapis/Join/Action | Felügyelt API csatlakozik. |
> | Műveletek | Microsoft.Web/Locations/managedapis/Read | Helyek felügyelt API-k lekérése. |
> | Műveletek | Microsoft.Web/Operations/Read | Műveletek beolvasása. |
> | Műveletek | Microsoft.Web/publishingusers/Read | Első közzététele a felhasználók. |
> | Műveletek | Microsoft.Web/publishingusers/Write | A frissítés közzététele a felhasználók. |
> | Műveletek | Microsoft.Web/recommendations/Read | Javaslatok az előfizetések listájának beolvasása. |
> | Műveletek | Microsoft.Web/register/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrálásához. |
> | Műveletek | Microsoft.Web/resourcehealthmetadata/Read | Resource Health metaadatainak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/Capabilities/Read | Az App Service-csomagok képességek beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/Delete | Egy meglévő App Service-csomag törlése |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | App Service-ben csomagok első nyilvános alkalmazások beállításainak törlése. |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | App Service-ben csomagok első nyilvános alkalmazások beállításainak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Frissítés App Service-csomagok első parti alkalmazásokra vonatkozó beállítások. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | App Service-ben csomagok hibrid kapcsolat névterek továbbítók törlése. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | App Service-ben csomagok hibrid kapcsolat névterek továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | App Service-ben csomagok hibrid kapcsolat névterek továbbítók webes alkalmazások beszerzéséhez. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | App Service-ben csomagok hibrid kapcsolat szolgáltatáscsomag korlátja beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | App Service-ben csomagok hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/metricdefinitions/Read | Az App Service-csomagok metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/Metrics/Read | Az App Service-csomagok metrikáinak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/operationresults/Read | Az App Service-csomagok művelet eredményeinek lekérése. |
> | Műveletek | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-csomag elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Web/serverfarms/Read | Az App Service-csomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/serverfarms/restartSites/Action | Indítsa újra az összes Web Apps az App Service-csomag |
> | Műveletek | Microsoft.Web/serverfarms/Sites/Read | App Service-csomagok webalkalmazások beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/skus/Read | Az App Service-csomagok termékváltozatok beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/usages/Read | Az App Service-csomagok Usages beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/Gateways/Write | Frissítse az App Service-ben csomagok virtuális hálózati kapcsolatok átjárót. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Az App Service-csomagok virtuális hálózati kapcsolatok lekérése. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/DELETE | Törölje az App Service-ben csomagok virtuális hálózati kapcsolatok útvonalak. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Read | App Service-ben csomagok virtuális hálózati kapcsolatok útvonalak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Write | App Service-ben csomagok virtuális hálózati kapcsolatok az útvonalak frissítése. |
> | Műveletek | Microsoft.Web/serverfarms/workers/reboot/Action | Indítsa újra az App Service-csomag feldolgozói. |
> | Műveletek | Microsoft.Web/serverfarms/Write | Hozzon létre egy új App Service-csomagot, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/Sites/analyzecustomhostname/Read | Elemezze az egyéni állomásnevet. |
> | Műveletek | Microsoft.Web/sites/applySlotConfig/Action | Webes alkalmazás tárolóhely tárolóhelyről származó konfiguráció cél egy aktuális webes alkalmazásban a alkalmazni |
> | Műveletek | Microsoft.Web/sites/backup/Action | Hozzon létre egy új webes alkalmazás biztonsági mentése |
> | Műveletek | Microsoft.Web/Sites/Backup/Read | Webalkalmazások biztonsági mentésének beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Backup/Write | Web Apps biztonsági másolat frissítéséhez. |
> | Műveletek | Microsoft.Web/Sites/backups/Action | Felderíti az alkalmazás meglévő biztonsági másolatát, amely az Azure storage-ból. |
> | Műveletek | Microsoft.Web/Sites/backups/DELETE | Delete Web Apps Backups. |
> | Műveletek | Microsoft.Web/Sites/backups/List/Action | Lista webes alkalmazások biztonsági mentése. |
> | Műveletek | Microsoft.Web/sites/backups/Read | Egy webalkalmazás biztonsági mentése tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/backups/restore/Action | Web Apps biztonsági másolatokat állíthatja vissza. |
> | Műveletek | Microsoft.Web/Sites/backups/Write | Frissítse a Web Apps biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/Sites/config/DELETE | Delete Web Apps Config. |
> | Műveletek | Microsoft.Web/sites/config/list/Action | Webalkalmazás biztonsági bizalmas beállítások, például közzétételi hitelesítő adatok, az alkalmazásbeállítások és a kapcsolati karakterláncok listázása |
> | Műveletek | Microsoft.Web/sites/config/Read | Webes alkalmazás konfigurációs beállításainak lekérése |
> | Műveletek | Microsoft.Web/sites/config/Write | Webes alkalmazás konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/Sites/containerlogs/Action | Tároló naplóinak beolvasása zip webalkalmazás. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/DELETE | Delete Web Apps Continuous Web Jobs. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/Read | Webes alkalmazások folyamatos Webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Indítsa el a Web Apps folyamatos Webjobs-feladatok. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/STOP/Action | Webes alkalmazások folyamatos Webjobs-feladatok leállítása. |
> | Műveletek | Microsoft.Web/sites/Delete | Meglévő webes alkalmazás törlése |
> | Műveletek | Microsoft.Web/Sites/Deployments/DELETE | Webes alkalmazások környezetet törli. |
> | Műveletek | Microsoft.Web/Sites/Deployments/log/Read | Webes alkalmazások központi telepítések napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Deployments/Read | Web Apps üzemelő példányok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Deployments/Write | Web Apps frissítéstelepítések. |
> | Műveletek | Microsoft.Web/Sites/detectors/Read | Web Apps érzékelők beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/analyses/execute/Action | Webes alkalmazások diagnosztikai elemzés futtatása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/analyses/Read | Webes alkalmazások diagnosztikai elemzési beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Szerezze be a webes alkalmazások diagnosztikai az ASP.NET Core-alkalmazást. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Webes alkalmazások diagnosztikai automatikus javítás funkció beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Deployment/Read | Webes alkalmazások diagnosztikai központi telepítő beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Webes alkalmazások diagnosztikai üzemelő példányok beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/detectors/execute/Action | Futtassa a Web Apps alkalmazások diagnosztikai detector használatával. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/detectors/Read | Webes alkalmazások diagnosztikai detector használatával kaphat. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Webes alkalmazások diagnosztikai sikertelen kérelmek száma az URI-t kaphat. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Webes alkalmazások diagnosztikai sikertelen kérelmek Eseménytárolási elemzés lekérése. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Webes alkalmazások diagnosztikai napló Analyzer beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Read | Webes alkalmazások diagnosztikai kategóriák beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Webes alkalmazások diagnosztikai modul elérhetőségének beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Webes alkalmazások diagnosztikai szolgáltatás állapotának beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Webes alkalmazások diagnosztikai hely CPU elemzése beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Webes alkalmazások diagnosztikai hely Szoftverleállások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Webes alkalmazások diagnosztikai hely késés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Webes alkalmazások diagnosztikai hely memória elemzés lekérése. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Webes alkalmazások diagnosztikai webhely újraindítása beállítás frissítésének letöltése. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Webes alkalmazások diagnosztikai webhely újraindítása felhasználó által kezdeményezett beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Webes alkalmazások diagnosztikai hely felcserélés első. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/THREADCOUNT/Read | Első webes alkalmazások diagnosztikai szálak száma. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Webes alkalmazások diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Webes alkalmazások diagnosztikai feldolgozó folyamat újrahasznosítási beolvasása. |
> | Műveletek | Microsoft.Web/Sites/domainownershipidentifiers/Read | Web Apps tartomány tulajdonjogának azonosítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/domainownershipidentifiers/Write | Frissítse a Web Apps tartomány tulajdonjogának azonosítók. |
> | Műveletek | Microsoft.Web/Sites/Functions/Action | Functions-webalkalmazásokhoz. |
> | Műveletek | Microsoft.Web/Sites/Functions/DELETE | Web Apps-függvényekre törlése. |
> | Műveletek | Microsoft.Web/Sites/Functions/listsecrets/Action | Lista titkok Web Apps-függvényekre. |
> | Műveletek | Microsoft.Web/Sites/Functions/masterkey/Read | Web Apps funkciók Masterkey beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Functions/Read | Web Apps-függvényekre beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Functions/token/Read | Get webes alkalmazások funkciók jogkivonat. |
> | Műveletek | Microsoft.Web/Sites/Functions/Write | Frissítse a Web Apps-függvényekre. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/DELETE | Delete Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/Read | Get Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/Write | Update Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/DELETE | Web Apps hibrid kapcsolat törlése. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/Read | Web Apps hibrid kapcsolat beolvasása. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/Write | Webes alkalmazások hibrid kapcsolat frissítése. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Web Apps a hibrid kapcsolat névterek továbbítók törlése. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Lista kulcsok Web Apps hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Web Apps a hibrid kapcsolat névterek továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Frissítse a Web Apps a hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionrelays/Read | Web Apps a hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Deployments/DELETE | Webes alkalmazások példányok környezetet törli. |
> | Műveletek | Microsoft.Web/Sites/Instances/Deployments/Read | Web Apps példányok üzemelő példányok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Extensions/log/Read | Web Apps példányok bővítmények napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Extensions/Read | Web Apps példányok kiterjesztések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Processes/DELETE | Törölje a Web Apps példányok folyamatokat. |
> | Műveletek | Microsoft.Web/Sites/Instances/Processes/Read | Web Apps példányok folyamatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Read | Web Apps-példány beolvasása. |
> | Műveletek | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista szinkronizálási függvény Trigger állapota Web Apps. |
> | Műveletek | Microsoft.Web/Sites/metricdefinitions/Read | Web Apps Metrikadefiníciók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Metrics/Read | Webes alkalmazások metrikáinak beolvasása. |
> | Műveletek | Microsoft.Web/Sites/metricsdefinitions/Read | Web Apps metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/migratemysql/Action | MySql Web Apps alkalmazások áttelepítése. |
> | Műveletek | Microsoft.Web/Sites/migratemysql/Read | Első webes alkalmazások Migrálása a MySql. |
> | Műveletek | Microsoft.Web/Sites/networktrace/Action | Hálózati nyomkövetési webalkalmazásokat. |
> | Műveletek | Microsoft.Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Műveletek | Microsoft.Web/Sites/operationresults/Read | Web Apps művelet eredményeinek lekérése. |
> | Műveletek | Microsoft.Web/Sites/Operations/Read | Web Apps-műveletek lekérése. |
> | Műveletek | Microsoft.Web/Sites/perfcounters/Read | Web Apps teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/DELETE | Web Apps a Premier szintű bővítmények törlése. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/Read | Web Apps a Premier szintű bővítmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/Write | Web Apps a Premier szintű bővítmények frissítése. |
> | Műveletek | Microsoft.Web/Sites/Processes/Read | Webes alkalmazásokat folyamatok beolvasása. |
> | Műveletek | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | microsoft.web/sites/providers/Microsoft.Insights/logDefinitions/read | A Web App elérhető naplók beolvasása |
> | Műveletek | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | A Web App elérhető metrikáinak lekérése |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/DELETE | Web Apps nyilvános tanúsítványok törlése. |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/Read | Web Apps nyilvános tanúsítványok megszerzéséhez. |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/Write | Web Apps nyilvános tanúsítványok frissítése. |
> | Műveletek | Microsoft.Web/sites/publish/Action | A webalkalmazás közzététele |
> | Műveletek | Microsoft.Web/sites/publishxml/Action | Közzétételi profil XML-fájlban egy webalkalmazás beolvasása |
> | Műveletek | Microsoft.Web/Sites/publishxml/Read | Web Apps-alkalmazások közzététele XML beolvasása. |
> | Műveletek | Microsoft.Web/sites/Read | Webes alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/recommendationhistory/Read | Web Apps javaslat előzmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/recommendations/disable/Action | Tiltsa le a Web Apps javaslatok. |
> | Műveletek | Microsoft.Web/sites/recommendations/Read | A web app javaslatok listájának beolvasása. |
> | Műveletek | Microsoft.Web/Sites/RECOVER/Action | Recover Web Apps. |
> | Műveletek | Microsoft.Web/sites/resetSlotConfig/Action | Webalkalmazás konfigurációjának visszaállítása |
> | Műveletek | Microsoft.Web/Sites/resourcehealthmetadata/Read | Web Apps-erőforrások állapotának metaadatainak beolvasása. |
> | Műveletek | Microsoft.Web/sites/restart/Action | Webalkalmazást indíthat újra |
> | Műveletek | Microsoft.Web/Sites/restore/Read | Webes alkalmazások visszaállítása beolvasása. |
> | Műveletek | Microsoft.Web/Sites/restore/Write | Webes alkalmazások visszaállítása. |
> | Műveletek | Microsoft.Web/Sites/restorefromdeletedwebapp/Action | Vissza a Web Apps, az alkalmazás törlése. |
> | Műveletek | Microsoft.Web/Sites/restoresnapshot/Action | Állítsa vissza a Web Apps pillanatképeket. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/DELETE | Web Apps Webhelybővítmények törlése. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/Read | Web Apps Webhelybővítmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/Write | Web Apps bővítmények frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Első webes alkalmazások tárhelyek elemzése egyéni állomásnevet. |
> | Műveletek | Microsoft.Web/sites/slots/applySlotConfig/Action | Webes alkalmazás tárolóhely tárolóhelyről származó konfiguráció cél a jelenlegi tárolóhelyen megadott a alkalmazni. |
> | Műveletek | Microsoft.Web/sites/slots/backup/Action | Hozzon létre új webes alkalmazás tárolóhely biztonsági mentést. |
> | Műveletek | Microsoft.Web/Sites/slots/Backup/Read | Webalkalmazások biztonsági mentésének pontok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Backup/Write | Webes alkalmazások tárhelyek biztonsági másolat frissítéséhez. |
> | Műveletek | Microsoft.Web/Sites/slots/backups/DELETE | Web Apps tárhelyek biztonsági másolatainak törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/backups/List/Action | Lista webes alkalmazások pontok biztonsági mentése. |
> | Műveletek | Microsoft.Web/sites/slots/backups/Read | A webalkalmazás tárolóhelyeinek biztonsági másolat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/slots/backups/restore/Action | Web Apps tárhelyek biztonsági másolatokat állíthatja vissza. |
> | Műveletek | Microsoft.Web/Sites/slots/config/DELETE | Törölje a Web Apps tárhelyek Config. |
> | Műveletek | Microsoft.Web/sites/slots/config/list/Action | Webes alkalmazás tárolóhely biztonsági bizalmas beállítások, például közzétételi hitelesítő adatok, az alkalmazásbeállítások és a kapcsolati karakterláncok listázása |
> | Műveletek | Microsoft.Web/sites/slots/config/Read | Webes alkalmazás a tárhely konfigurációja beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/config/Write | Webes alkalmazás pont konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/DELETE | Web Apps tárhelyek folyamatos Webjobs-feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Web Apps tárhelyek folyamatos Webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Indítsa el a Web Apps tárhelyek folyamatos Webjobs-feladatok. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/STOP/Action | Web Apps tárhelyek folyamatos Webjobs-feladatok leállítása. |
> | Műveletek | Microsoft.Web/sites/slots/Delete | Törölje a meglévő webes alkalmazás tárhelyek |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/DELETE | Törölje a Web Apps tárolóhelyek központi telepítések. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/log/Read | Web Apps tárolóhelyek központi telepítések napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/Read | Web Apps tárhelyek üzemelő példányok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/Write | Web Apps tárhelyek frissítéstelepítések. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Web Apps tárhelyek diagnosztikai elemzés futtatása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Web Apps tárhelyek diagnosztikai elemzési beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Szerezze be a Web Apps tárhelyek diagnosztikai az ASP.NET Core-alkalmazás. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Web Apps tárhelyek diagnosztika automatikus javítás funkció beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | Web Apps tárhelyek diagnosztikai központi telepítő beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Web Apps tárhelyek diagnosztikai üzemelő példányok beolvasása. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Futtassa a Web Apps tárhelyek diagnosztikai detector használatával. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Web Apps tárhelyek diagnosztikai detector használatával kaphat. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Web Apps tárhelyek diagnosztikai sikertelen kérelmek Eseménytárolási elemzési beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Webes alkalmazások tárhelyek diagnosztikai napló Analyzer beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Read | Web Apps tárhelyek Diagnostics beszerzése. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Web Apps tárhelyek diagnosztikai modul elérhetőségének beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Webes alkalmazások tárhelyek diagnosztikai szolgáltatásállapot beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Webes alkalmazások tárhelyek diagnosztikai hely CPU elemzése beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Web Apps tárhelyek diagnosztikai hely Szoftverleállások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Web Apps tárhelyek diagnosztikai hely késés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Web Apps tárhelyek diagnosztikai hely memória Analysis beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Webes alkalmazások tárhelyek diagnosztikai webhely újraindítása beállítás frissítésének letöltése. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Webes alkalmazások tárhelyek diagnosztikai webhely újraindítása felhasználó által kezdeményezett beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Web Apps tárhelyek diagnosztikai hely lapozófájl-kapacitás beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/THREADCOUNT/Read | Első webes alkalmazások tárhelyek diagnosztikai szálak száma. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Web Apps tárhelyek diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Web Apps tárhelyek diagnosztikai munkavégző folyamat újrahasznosítását beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Webes alkalmazások tárhelyek tartomány tulajdonjogának azonosítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/DELETE | Delete Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/Read | Get Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/Write | Update Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/DELETE | Web Apps tárhelyek hibrid kapcsolat törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/Read | Web Apps tárhelyek hibrid kapcsolat beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/Write | Webes alkalmazások tárhelyek hibrid kapcsolat frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/DELETE | Web Apps tárhelyek hibrid kapcsolat névterek továbbítók törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Frissítse a Web Apps tárhelyek hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Web Apps tárhelyek hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Deployments/Read | Web Apps tárhelyek példányok üzemelő példányok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Processes/DELETE | Törölje a Web Apps tárhelyek példányok folyamatokat. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Processes/Read | Web Apps tárhelyek példányok folyamatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Read | Web Apps tárhelyek példányok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/metricdefinitions/Read | Web Apps tárhelyek metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Metrics/Read | Web Apps tárhelyek metrikáinak beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/migratemysql/Read | Első webes alkalmazások pontok áttelepítése a MySql. |
> | Műveletek | Microsoft.Web/Sites/slots/networktrace/Action | Hálózati nyomkövetési Web Apps tárolóhelyek. |
> | Műveletek | Microsoft.Web/Sites/slots/newpassword/Action | Newpassword Web Apps tárolóhelyek. |
> | Műveletek | Microsoft.Web/Sites/slots/operationresults/Read | Web Apps tárhelyek művelet eredményeinek lekérése. |
> | Műveletek | Microsoft.Web/Sites/slots/Operations/Read | Web Apps tárhelyek műveletek beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/perfcounters/Read | Web Apps tárhelyek teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/phplogging/Read | Web Apps tárhelyek Phplogging beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/DELETE | Web Apps tárhelyek Premier szintű bővítmények törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/Read | Web Apps tárhelyek Premier szintű bővítmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/Write | Web Apps tárhelyek Premier szintű bővítmények frissítése. |
> | Műveletek | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | microsoft.web/sites/slots/providers/Microsoft.Insights/logDefinitions/read | Webalkalmazás tárhelyeinek naplók beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Elérhető metrikáinak lekérése a webes alkalmazás üzembe helyezési pont |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/DELETE | Webes alkalmazások tárolóhely nyilvános tanúsítványok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/Read | Webes alkalmazások tárolóhely nyilvános tanúsítványok megszerzéséhez. |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/Write | Hozzon létre vagy nem frissíthető a Web Apps tárolóhely nyilvános tanúsítványokat. |
> | Műveletek | Microsoft.Web/sites/slots/publish/Action | A Web App tárolóhelyet közzététele |
> | Műveletek | Microsoft.Web/sites/slots/publishxml/Action | Közzétételi profil XML-fájlban webes alkalmazás üzembe helyezési pont lekérése |
> | Műveletek | Microsoft.Web/sites/slots/Read | A webes alkalmazás üzembe helyezési pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/slots/RECOVER/Action | Web Apps tárhelyek helyre. |
> | Műveletek | Microsoft.Web/sites/slots/resetSlotConfig/Action | Webalkalmazás konfigurációjának tárolóhely alaphelyzetbe állítása |
> | Műveletek | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Webes alkalmazások tárhelyek Resource Health metaadatainak beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/restart/Action | Indítsa újra a Web App tárolóhelyet |
> | Műveletek | Microsoft.Web/Sites/slots/restore/Read | Web Apps tárhelyek visszaállítási beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/restore/Write | Webalkalmazások alkalmazások pontjainak visszaállítása. |
> | Műveletek | Microsoft.Web/Sites/slots/restorefromdeletedwebapp/Action | Webalkalmazás tárolóhelyeinek vissza az alkalmazás törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/restoresnapshot/Action | Állítsa vissza a Web Apps tárhelyek pillanatképeket. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/DELETE | Web Apps tárhelyek Webhelybővítmények törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/Read | Web Apps tárhelyek Webhelybővítmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/Write | Web Apps tárhelyek bővítmények frissítése. |
> | Műveletek | Microsoft.Web/sites/slots/slotsdiffs/Action | Első konfiguráció web app és a tárhelyek közötti különbségek |
> | Műveletek | Microsoft.Web/sites/slots/slotsswap/Action | Webalkalmazás üzembehelyezési pontok cseréje |
> | Műveletek | Microsoft.Web/Sites/slots/snapshots/Read | Web Apps tárhelyek pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Delete | Webes alkalmazás tárolóhely forrás a konfigurációs beállítások törlése |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Read | Webes alkalmazás tárolóhely verziókövetési konfiguráció beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Write | Webes alkalmazás tárolóhely forrás a konfigurációs beállítások frissítése |
> | Műveletek | Microsoft.Web/sites/slots/start/Action | Indítsa el a Web App tárolóhelyet |
> | Műveletek | Microsoft.Web/sites/slots/stop/Action | Állítsa le a webes alkalmazás tárolóhelyet |
> | Műveletek | Microsoft.Web/Sites/slots/Sync/Action | Szinkronizálási Web Apps tárolóhelyek. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/DELETE | Web Apps tárhelyek aktivált webjobs-feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Web Apps tárhelyek aktivált webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Web Apps tárhelyek aktivált webjobs-feladatok futtatásához. |
> | Műveletek | Microsoft.Web/Sites/slots/usages/Read | Web Apps tárhelyek Usages beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/DELETE | Web Apps tárolóhelyek virtuális hálózati kapcsolatok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Gateways/Write | Web Apps tárolóhelyek virtuális hálózati kapcsolatok átjárók frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Web Apps tárolóhelyek virtuális hálózati kapcsolatok lekérése. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Web Apps tárolóhelyek virtuális hálózati kapcsolatának frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/webjobs/Read | Web Apps tárhelyek webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/Write | Webes alkalmazás új tárhely létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/sites/slotsdiffs/Action | Első konfiguráció web app és a tárhelyek közötti különbségek |
> | Műveletek | Microsoft.Web/sites/slotsswap/Action | Webalkalmazás üzembehelyezési pontok cseréje |
> | Műveletek | Microsoft.Web/Sites/snapshots/Read | Web Apps pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Delete | Webes alkalmazás forrás a konfigurációs beállítások törlése |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Read | Webes alkalmazás verziókövetési konfiguráció beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Write | Webes alkalmazás forrás a konfigurációs beállítások frissítése |
> | Műveletek | Microsoft.Web/sites/start/Action | Webalkalmazás indítása |
> | Műveletek | Microsoft.Web/sites/stop/Action | Webalkalmazás leállítása |
> | Műveletek | Microsoft.Web/Sites/Sync/Action | Sync Web Apps. |
> | Műveletek | Microsoft.Web/Sites/syncfunctiontriggers/Action | A Web Apps szinkronizálása a függvény eseményindítóit. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Web Apps aktivált webjobs-feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Web Apps aktivált webjobs-feladatok előzményeinek beolvasása. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/Read | Web Apps aktivált webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Web Apps aktivált webjobs-feladatok futtatásához. |
> | Műveletek | Microsoft.Web/Sites/usages/Read | Web Apps Usages beolvasása. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Web Apps virtuális hálózati kapcsolatok törlése. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Read | Web Apps virtuális hálózati kapcsolatok átjárók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Write | Web Apps virtuális hálózati kapcsolatok átjárók frissítése. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Read | Web Apps virtuális hálózati kapcsolatok lekérése. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Write | Web Apps virtuális hálózati kapcsolatának frissítése. |
> | Műveletek | Microsoft.Web/Sites/webjobs/Read | Web Apps webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/Write | Hozzon létre egy új webalkalmazást, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/skus/Read | Termékváltozatok beolvasása. |
> | Műveletek | Microsoft.Web/sourcecontrols/Read | Adatforrás-vezérlők beolvasása. |
> | Műveletek | Microsoft.Web/sourcecontrols/Write | Frissítse az adatforrás-vezérlők. |
> | Műveletek | Microsoft.Web/unregister/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrációját. |
> | Műveletek | Microsoft.Web/Validate/Action | Ellenőrzése. |
> | Műveletek | Microsoft.Web/verifyhostingenvironmentvnet/Action | Ellenőrizze az üzemeltetési környezet Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.WorkloadMonitor/components/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/healthInstances/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/Operations/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/delete | Töröl egy workloadInsights erőforrást |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/delete | Töröl egy workloadInsights erőforrást |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/read | WorkloadInsights erőforrás beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/read | WorkloadInsights erőforrás beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/write | Ír workloadInsights erőforrás |
> | Műveletek | Microsoft.WorkloadMonitor/workloadInsights/write | Ír workloadInsights erőforrás |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/delete |  |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/delete |  |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/read | A számítási erőforrás beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/read | A számítási erőforrás beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/write | Ír egy számítási erőforrás |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/write | Ír egy számítási erőforrás |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök](custom-roles.md)
- [Beépített szerepkörök](built-in-roles.md)
